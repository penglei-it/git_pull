*&---------------------------------------------------------------------*
*& 获取微信公众号用户 OpenID 的完整示例
*&---------------------------------------------------------------------*
*&
*& 说明：获取 OpenID 需要两步
*& 1. 生成授权 URL，引导用户访问
*& 2. 用户授权后，微信回调你的服务器，通过 code 获取 OpenID
*&
*&---------------------------------------------------------------------*

"============================================================================
"方式一：通过网页授权获取 OpenID（推荐）
"============================================================================

"步骤1：生成授权 URL，引导用户点击访问
DATA: lv_appid        TYPE string,
      lv_redirect_uri TYPE string,
      lv_auth_url     TYPE string,
      lv_state        TYPE string.

"从配置表读取 AppID
SELECT SINGLE appid INTO lv_appid
  FROM zticerp_wechat_config
  WHERE config_key = 'OFFICIAL_ACCOUNT'.

"设置回调地址（必须是在微信公众平台配置的授权回调域名下的页面）
"例如：https://yourdomain.com/wechat/callback
"注意：这个 URL 必须是已经在微信公众平台后台配置的授权回调域名
lv_redirect_uri = 'https://yourdomain.com/wechat/oauth_callback'.

"可选：设置 state 参数（用于防止 CSRF 攻击，建议使用用户 ID）
lv_state = |USER_{ ls_user-userid }|.

"生成授权 URL
lv_auth_url = zcl_wechat_official_api=>get_oauth_url(
  iv_appid        = lv_appid
  iv_redirect_uri = lv_redirect_uri
  iv_state        = lv_state
  iv_scope        = 'snsapi_base'  "静默授权，只需 openid
).

"将授权 URL 返回给前端，用户点击后会跳转到微信授权页面
"授权成功后，微信会重定向到 redirect_uri，并带上 code 和 state 参数
"例如：https://yourdomain.com/wechat/oauth_callback?code=CODE123&state=USER_001


"步骤2：处理回调，通过 code 获取 OpenID
"（通常在 HTTP 服务或 Web 应用中处理回调请求）

DATA: lv_code         TYPE string,
      lv_appsecret    TYPE string,
      lv_openid       TYPE string,
      lv_userid       TYPE string.

"从回调 URL 中获取 code 和 state
"例如：从 HTTP 请求参数中获取
lv_code = request->get_form_field( 'code' ).
lv_state = request->get_form_field( 'state' ).

"从 state 中提取用户 ID（如果之前设置了）
IF lv_state CS 'USER_'.
  REPLACE 'USER_' IN lv_state WITH ''.
  lv_userid = lv_state.
ENDIF.

"从配置表读取 AppSecret
SELECT SINGLE appsecret INTO lv_appsecret
  FROM zticerp_wechat_config
  WHERE config_key = 'OFFICIAL_ACCOUNT'.

"通过 code 获取 OpenID
lv_openid = zcl_wechat_official_api=>get_openid_by_code(
  iv_appid     = lv_appid
  iv_appsecret = lv_appsecret
  iv_code      = lv_code
).

"步骤3：保存 OpenID 到数据库
IF lv_openid IS NOT INITIAL AND lv_userid IS NOT INITIAL.
  UPDATE ztint_user_inf
    SET wechat_openid = lv_openid
        zupd_bdate    = sy-datum
        zupd_btime    = sy-uzeit
    WHERE userid = lv_userid.
  
  IF sy-subrc = 0.
    MESSAGE 'OpenID 获取并保存成功' TYPE 'S'.
  ELSE.
    MESSAGE '保存 OpenID 失败' TYPE 'E'.
  ENDIF.
ELSE.
  MESSAGE '获取 OpenID 失败' TYPE 'E'.
ENDIF.


"============================================================================
"方式二：在 SAP 系统中直接获取授权 URL（适用于内嵌网页）
"============================================================================

"示例：在 SAP WebDynpro 或 FPM 应用中

DATA: lv_appid TYPE string,
      lv_url   TYPE string.

SELECT SINGLE appid INTO lv_appid
  FROM zticerp_wechat_config
  WHERE config_key = 'OFFICIAL_ACCOUNT'.

"生成授权 URL（回调到 SAP 系统的 Web 服务）
lv_url = zcl_wechat_official_api=>get_oauth_url(
  iv_appid        = lv_appid
  iv_redirect_uri  = 'https://your-sap-server:port/sap/bc/webdynpro/sap/zwechat_callback'
  iv_state        = sy-uname  "使用当前用户
  iv_scope        = 'snsapi_base'
).

"在前端页面中显示二维码或链接，用户扫描/点击后授权
"例如：生成二维码或直接跳转


"============================================================================
"方式三：批量获取 OpenID（适用于有用户列表的场景）
"============================================================================

"如果需要在某个页面让多个用户授权获取 OpenID
"可以生成一个包含用户信息的授权链接列表

DATA: lt_users TYPE STANDARD TABLE OF ztint_user_inf,
      ls_user  LIKE LINE OF lt_users,
      lv_url   TYPE string.

SELECT * FROM ztint_user_inf
  INTO TABLE lt_users
  WHERE wechat_openid IS INITIAL.

SELECT SINGLE appid INTO lv_appid
  FROM zticerp_wechat_config
  WHERE config_key = 'OFFICIAL_ACCOUNT'.

LOOP AT lt_users INTO ls_user.
  "为每个用户生成授权 URL
  lv_url = zcl_wechat_official_api=>get_oauth_url(
    iv_appid        = lv_appid
    iv_redirect_uri  = |https://yourdomain.com/wechat/callback|
    iv_state        = |USER_{ ls_user-userid }|
    iv_scope        = 'snsapi_base'
  ).
  
  "可以保存到表或发送给用户
  "例如：发送包含授权链接的消息给用户
ENDLOOP.


"============================================================================
"注意事项：
"============================================================================
"
"1. 授权回调域名配置：
"   - 登录微信公众平台 -> 开发 -> 接口权限 -> 网页授权获取用户基本信息
"   - 设置授权回调域名（不需要 http:// 前缀，例如：yourdomain.com）
"
"2. redirect_uri 必须是授权回调域名下的页面，否则会报错
"
"3. code 有效期：用户授权后返回的 code 只能使用一次，5 分钟内有效
"
"4. scope 参数：
"   - snsapi_base：静默授权，用户无感知，只能获取 openid
"   - snsapi_userinfo：需要用户确认，可以获取用户昵称、头像等信息
"
"5. state 参数：
"   - 建议使用，用于防止 CSRF 攻击
"   - 可以传递业务参数（如用户 ID），回调时用于关联
"
"6. 生产环境建议：
"   - 使用 HTTPS
"   - 验证 state 参数
"   - 记录授权日志
"   - 处理授权失败的情况
"
"============================================================================

