*&---------------------------------------------------------------------*
*& 微信公众号 API 使用示例（参考 ZCL_CORPWEIXIN_APPROVAL_API）
*&---------------------------------------------------------------------*
*&
*& 说明：已添加 CONSTRUCTOR 和 GET_WECHAT_TOKEN 方法
*& 使用方式与 ZCL_CORPWEIXIN_APPROVAL_API 类似
*&
*&---------------------------------------------------------------------*

"============================================================================
"方式一：使用实例方法（推荐，参考 ZCL_CORPWEIXIN_APPROVAL_API）
"============================================================================

"1. 创建实例并初始化（类似 ZCL_CORPWEIXIN_APPROVAL_API 的用法）
DATA: lo_wechat_api TYPE REF TO zcl_wechat_official_api.

"方式A：传入配置参数
lo_wechat_api = NEW zcl_wechat_official_api(
  iv_appid      = 'your_appid'
  iv_appsecret  = 'your_appsecret'
  iv_destination = 'WECHAT_OFFICIAL_API'  "可选，SM59 中的 destination
).

"方式B：不传参数，从配置表读取（类似 ZCL_CORPWEIXIN_APPROVAL_API）
lo_wechat_api = NEW zcl_wechat_official_api( ).
"会自动从 zticerp_wechat_config 表读取配置

"2. 获取 Access Token（类似 get_corpwx_token）
DATA: lv_token TYPE string.

"使用实例方法获取 token（支持重试机制，最多 5 次）
lv_token = lo_wechat_api->get_wechat_token( ).

IF lv_token IS NOT INITIAL.
  "3. 发送消息
  DATA(lv_openid) = 'user_openid'.
  DATA(lv_content) = '测试消息'.
  
  DATA(lv_success) = zcl_wechat_official_api=>send_text_message(
    iv_access_token = lv_token
    iv_openid       = lv_openid
    iv_content      = lv_content
  ).
ENDIF.


"============================================================================
"方式二：完整的业务流程示例（参考 ZCL_CORPWEIXIN_APPROVAL_API）
"============================================================================

"类似 get_approval_detail_by_spno 方法的用法
METHOD send_risk_notification.
  DATA: lo_wechat_api TYPE REF TO zcl_wechat_official_api,
        lv_token      TYPE string,
        lv_openid     TYPE string,
        lv_success    TYPE abap_bool.

  "创建实例（使用 destination）
  lo_wechat_api = NEW zcl_wechat_official_api(
    iv_destination = 'WECHAT_OFFICIAL_API'
  ).

  "获取 token（支持重试，最多 5 次，类似 get_corpwx_token）
  DO 5 TIMES.
    lv_token = lo_wechat_api->get_wechat_token( ).
    IF lv_token IS NOT INITIAL.
      EXIT.
    ENDIF.
    "如果失败，等待后重试
    IF sy-index < 5.
      WAIT UP TO 1 SECONDS.
    ENDIF.
  ENDDO.

  IF lv_token IS INITIAL.
    MESSAGE '获取微信 Token 失败' TYPE 'E'.
    RETURN.
  ENDIF.

  "获取用户 OpenID
  SELECT SINGLE wechat_openid INTO lv_openid
    FROM ztint_user_inf
    WHERE userid = iv_userid
      AND wechat_openid IS NOT INITIAL.

  IF lv_openid IS NOT INITIAL.
    "发送文本消息
    lv_success = zcl_wechat_official_api=>send_text_message(
      iv_access_token = lv_token
      iv_openid      = lv_openid
      iv_content     = iv_content
    ).
    
    "发送图文消息
    IF lv_success = abap_true.
      DATA(lt_articles) = VALUE zcl_wechat_official_api=>ty_article_tab(
        ( title       = '高风险件报备'
          description = |订单号：{ iv_orderid }|
          url         = |https://yourdomain.com/detail/{ iv_orderid }|
          picurl      = 'https://example.com/image.jpg' )
      ).
      
      lv_success = zcl_wechat_official_api=>send_news_message(
        iv_access_token = lv_token
        iv_openid      = lv_openid
        it_articles    = lt_articles
      ).
    ENDIF.
  ENDIF.

ENDMETHOD.


"============================================================================
"方式三：与 ZCL_CORPWEIXIN_APPROVAL_API 对比使用
"============================================================================

"企业微信 API 的用法（参考）
"DATA(lo_qywx) = NEW zcl_corpweixin_api( appname = 'CASSINTQYWX' destination = 'QYWX' ).
"DATA(lv_token) = lo_qywx->get_corpwx_token( ).

"微信公众号 API 的用法（新增，类似）
DATA(lo_wechat) = NEW zcl_wechat_official_api(
  iv_destination = 'WECHAT_OFFICIAL_API'
).
DATA(lv_wechat_token) = lo_wechat->get_wechat_token( ).

"两者使用方式一致，便于代码维护和理解


"============================================================================
"方式四：使用配置表（推荐用于生产环境）
"============================================================================

"1. 确保配置表 zticerp_wechat_config 中有配置
"   INSERT INTO zticerp_wechat_config VALUES (
"     config_key  = 'OFFICIAL_ACCOUNT'
"     appid       = 'your_appid'
"     appsecret   = 'your_appsecret'
"   ).

"2. 创建实例时不传参数，自动从配置表读取
DATA(lo_wechat_api) = NEW zcl_wechat_official_api( ).

"3. 获取 token
DATA(lv_token) = lo_wechat_api->get_wechat_token( ).

"优势：
"   - 配置集中管理
"   - 无需在代码中硬编码敏感信息
"   - 便于维护和更新


"============================================================================
"方式五：向后兼容（仍然可以使用静态方法）
"============================================================================

"原有的静态方法仍然可用，保持向后兼容
DATA(lv_static_token) = zcl_wechat_official_api=>get_access_token(
  iv_appid     = 'your_appid'
  iv_appsecret = 'your_appsecret'
).

DATA(lv_static_success) = zcl_wechat_official_api=>send_text_message(
  iv_access_token = lv_static_token
  iv_openid      = 'user_openid'
  iv_content     = '消息内容'
).


"============================================================================
"注意事项：
"============================================================================
"
"1. CONSTRUCTOR 参数说明：
"   - iv_appid: 微信公众号 AppID（可选，可从配置表读取）
"   - iv_appsecret: 微信公众号 AppSecret（可选，可从配置表读取）
"   - iv_destination: SM59 中配置的 HTTP 目标（可选，默认 WECHAT_OFFICIAL_API）
"
"2. get_wechat_token 方法：
"   - 自动使用构造函数中设置的 appid 和 appsecret
"   - 支持重试机制（最多 5 次）
"   - 使用实例缓存，提高性能
"
"3. 与 ZCL_CORPWEIXIN_APPROVAL_API 的区别：
"   - 企业微信使用 get_corpwx_token()
"   - 微信公众号使用 get_wechat_token()
"   - 两者使用方式完全一致，便于代码统一
"
"4. 推荐用法：
"   - 生产环境：使用配置表方式
"   - 测试环境：可以直接传入参数
"   - 批量处理：创建实例后复用
"
"============================================================================

