*&---------------------------------------------------------------------*
*& 修改后的发送逻辑（在原有代码基础上添加）
*&---------------------------------------------------------------------*

"在原有发送企微消息的代码后面添加以下代码

"1. 将 Markdown 转换为微信文本格式
DATA(lv_wechat_text) = zcl_wechat_msg_converter=>markdown_to_text( ls_sendmsg-content ).

"2. 添加转发标识
lv_wechat_text = lv_wechat_text && 
  cl_abap_char_utilities=>newline && 
  '━━━━━━━━━━━━━━━━' &&
  cl_abap_char_utilities=>newline &&
  '📱 来自企微群机器人' &&
  cl_abap_char_utilities=>newline &&
  '⚠️ 已转换为微信兼容格式'.

"3. 获取微信公众号配置
DATA: lv_wechat_appid     TYPE string,
      lv_wechat_appsecret TYPE string,
      lv_wechat_openid     TYPE string.

"从配置表或自定义表读取微信公众号配置
SELECT SINGLE appid appsecret INTO (lv_wechat_appid, lv_wechat_appsecret)
  FROM zticerp_wechat_config
  WHERE config_key = 'OFFICIAL_ACCOUNT'.

"获取目标用户微信 OpenID（根据业务用户映射）
"OpenID 说明：微信公众号平台中用户的唯一标识符，通过 OAuth 网页授权获取
"每个关注公众号的用户都有一个唯一的 OpenID，用于标识和发送消息给该用户
"需要在用户授权时将 OpenID 保存到 ztint_user_inf 表的 wechat_openid 字段
SELECT SINGLE wechat_openid INTO lv_wechat_openid
  FROM ztint_user_inf
  WHERE userid = ls_hriskwo_h-followuserid
    AND wechat_openid IS NOT INITIAL.

"4. 发送到微信（如果配置了微信参数）
IF lv_wechat_appid IS NOT INITIAL 
   AND lv_wechat_appsecret IS NOT INITIAL
   AND lv_wechat_openid IS NOT INITIAL.

  DATA: lv_access_token TYPE string,
        lv_success      TYPE abap_bool.

  "获取 Access Token
  lv_access_token = zcl_wechat_official_api=>get_access_token(
    iv_appid     = lv_wechat_appid
    iv_appsecret = lv_wechat_appsecret
  ).

  IF lv_access_token IS NOT INITIAL.
    "发送文本消息到微信
    lv_success = zcl_wechat_official_api=>send_text_message(
      iv_access_token = lv_access_token
      iv_openid       = lv_wechat_openid
      iv_content      = lv_wechat_text
    ).

    IF lv_success = abap_true.
      MESSAGE '消息已同步发送到微信' TYPE 'S'.
    ELSE.
      MESSAGE '微信消息发送失败' TYPE 'W'.
    ENDIF.
  ELSE.
    "记录错误但不中断主流程
    MESSAGE '获取微信 Access Token 失败' TYPE 'W'.
  ENDIF.

ENDIF.

"原有的企微消息发送逻辑继续执行
lo_notice->set_robot_message(
  EXPORTING
    iv_key  = lv_key
    sendmsg = ls_sendmsg ).