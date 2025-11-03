*&---------------------------------------------------------------------*
*& 方案：在 Markdown 消息中添加链接，点击后发送图文消息
*&---------------------------------------------------------------------*
*&
*& 说明：
*& 1. 企业微信机器人消息是单向的，无法直接响应点击事件
*& 2. 但可以在 Markdown 中添加链接，链接跳转到 Web 页面
*& 3. 在 Web 页面中触发回调，发送图文消息给操作人
*&
*&---------------------------------------------------------------------*

"============================================================================
"方案一：通过链接跳转触发发送图文消息（推荐）
"============================================================================

"步骤1：在发送 Markdown 消息时，添加一个"发送图文消息"的链接

"在 send_qw.md 的第 733-734 行之后添加以下代码：

"添加"发送图文消息"链接（在"立即处理"链接之后）
DATA: lv_news_callback_url TYPE string,
      lv_userid_for_news    TYPE string.

"构建回调 URL，用于触发发送图文消息
"参数说明：
" - woid: 工单号
" - userid: 操作人用户ID
" - action: 操作类型（send_news）
lv_userid_for_news = ls_hriskwo_h-followuserid.
lv_news_callback_url = |{ lv_hostname }/sap/bc/webdynpro/sap/zwechat_send_news| &&
                       |?woid={ ls_hriskwo_h-woid }| &&
                       |&userid={ lv_userid_for_news }| &&
                       |&action=send_news| &&
                       |&msgid={ lv_messageid }|.

"在 Markdown 消息中添加"发送图文消息"链接
IF lv_news_callback_url IS NOT INITIAL.
  ls_sendmsg-content = ls_sendmsg-content &&
    |  \\n  >[📱 发送图文消息]({ lv_news_callback_url })|.
ENDIF.


"============================================================================
"步骤2：创建回调处理函数（Function Module 或 Class Method）
"============================================================================

*&---------------------------------------------------------------------*
*& Function Module: Z_FMINT_SEND_NEWS_TO_USER
*&---------------------------------------------------------------------*
*& 功能：根据 URL 参数发送图文消息给指定用户
*&
*& 参数：
*&   IV_WOID    - 工单号
*&   IV_USERID  - 用户ID
*&   IV_MSGID   - 消息ID（可选）
*&
*&---------------------------------------------------------------------*
FUNCTION z_fmint_send_news_to_user.
*"----------------------------------------------------------------------
*"*"本地接口：
*"  IMPORTING
*"     VALUE(IV_WOID) TYPE STRING
*"     VALUE(IV_USERID) TYPE STRING
*"     VALUE(IV_MSGID) TYPE STRING OPTIONAL
*"  EXPORTING
*"     VALUE(EV_SUCCESS) TYPE ABAP_BOOL
*"     VALUE(EV_MSG) TYPE STRING
*"----------------------------------------------------------------------

  DATA: ls_hriskwo_h      TYPE ztint_hriskwo_h,
        ls_user           TYPE ztint_user_inf,
        lv_appid          TYPE string,
        lv_appsecret       TYPE string,
        lv_openid          TYPE string,
        lv_access_token    TYPE string,
        lt_articles        TYPE zcl_wechat_official_api=>ty_article_tab,
        ls_article         LIKE LINE OF lt_articles,
        lv_hostname        TYPE string,
        lv_detail_url      TYPE string,
        lv_image_url       TYPE string,
        lv_success          TYPE abap_bool.

  "1. 获取工单信息
  SELECT SINGLE * FROM ztint_hriskwo_h INTO ls_hriskwo_h
    WHERE woid = iv_woid.
  
  IF sy-subrc <> 0.
    ev_success = abap_false.
    ev_msg = '工单不存在'.
    RETURN.
  ENDIF.

  "2. 获取用户信息（包括微信 OpenID）
  SELECT SINGLE * FROM ztint_user_inf INTO ls_user
    WHERE userid = iv_userid.
  
  IF sy-subrc <> 0.
    ev_success = abap_false.
    ev_msg = '用户不存在'.
    RETURN.
  ENDIF.

  "检查是否有微信 OpenID
  IF ls_user-wechat_openid IS INITIAL.
    ev_success = abap_false.
    ev_msg = '用户未绑定微信 OpenID，无法发送图文消息'.
    RETURN.
  ENDIF.
  lv_openid = ls_user-wechat_openid.

  "3. 获取微信公众号配置
  SELECT SINGLE appid appsecret INTO (lv_appid, lv_appsecret)
    FROM zticerp_wechat_config
    WHERE config_key = 'OFFICIAL_ACCOUNT'.
  
  IF lv_appid IS INITIAL OR lv_appsecret IS INITIAL.
    ev_success = abap_false.
    ev_msg = '微信公众号配置不存在'.
    RETURN.
  ENDIF.

  "4. 获取 Access Token
  lv_access_token = zcl_wechat_official_api=>get_access_token(
    iv_appid     = lv_appid
    iv_appsecret = lv_appsecret
  ).
  
  IF lv_access_token IS INITIAL.
    ev_success = abap_false.
    ev_msg = '获取 Access Token 失败'.
    RETURN.
  ENDIF.

  "5. 构建图文消息内容
  "获取系统域名（用于构建详情链接）
  SELECT SINGLE mobileurl INTO lv_hostname
    FROM zticerp_ding_sk AS a
    INNER JOIN zticerp_ding_gid AS g ON a~gentid = g~gentid
    WHERE g~fname = 'CASSINTQYWX'.
  
  lv_detail_url = |{ lv_hostname }#/newRiskSheet/detail/{ iv_woid }|.

  "设置图片 URL（可以使用工单相关的图片或默认图片）
  lv_image_url = 'https://img1.baidu.com/it/u=2521516110,1741003725&fm=253&fmt=auto&app=138&f=JPEG?w=800&h=500'.

  "构建第一条图文消息（主消息）
  ls_article-title = |高风险件报备 - { ls_hriskwo_h-orderid }|.
  ls_article-description = |订单号：{ ls_hriskwo_h-orderid } | &&
                           |维修厂：{ ls_hriskwo_h-cusname } | &&
                           |下单时间：{ ls_hriskwo_h-orderdate DATE = ISO }|.
  ls_article-url = lv_detail_url.
  ls_article-picurl = lv_image_url.
  APPEND ls_article TO lt_articles.

  "如果有多个零件，可以添加多条图文消息
  "例如：添加零件详情
  IF ls_hriskwo_h-partsname IS NOT INITIAL.
    CLEAR ls_article.
    ls_article-title = |风险零件详情|.
    ls_article-description = |零件名称：{ ls_hriskwo_h-partsname }| &&
                             |车辆品牌：{ ls_hriskwo_h-carbrandname }|.
    ls_article-url = lv_detail_url.
    APPEND ls_article TO lt_articles.
  ENDIF.

  "6. 发送图文消息
  lv_success = zcl_wechat_official_api=>send_news_message(
    iv_access_token = lv_access_token
    iv_openid        = lv_openid
    it_articles      = lt_articles
  ).

  IF lv_success = abap_true.
    ev_success = abap_true.
    ev_msg = '图文消息发送成功'.
  ELSE.
    ev_success = abap_false.
    ev_msg = '图文消息发送失败'.
  ENDIF.

ENDFUNCTION.


"============================================================================
"步骤3：在 WebDynpro 或 Web Service 中处理回调请求
"============================================================================

*&---------------------------------------------------------------------*
*& WebDynpro Component: ZWECHAT_SEND_NEWS
*&---------------------------------------------------------------------*
*& 说明：创建一个简单的 WebDynpro 组件来处理回调
*& 或者使用 HTTP Service Handler
*&---------------------------------------------------------------------*

METHOD on_request.
  "获取 URL 参数
  DATA: lv_woid   TYPE string,
        lv_userid TYPE string,
        lv_action TYPE string,
        lv_msgid  TYPE string,
        lv_success TYPE abap_bool,
        lv_msg     TYPE string,
        lv_result  TYPE string.

  "从 URL 参数中获取值
  lv_woid   = request->get_form_field( 'woid' ).
  lv_userid = request->get_form_field( 'userid' ).
  lv_action = request->get_form_field( 'action' ).
  lv_msgid  = request->get_form_field( 'msgid' ).

  "验证参数
  IF lv_woid IS INITIAL OR lv_userid IS INITIAL.
    lv_result = |{"success":false,"msg":"参数不完整"}|.
    response->set_cdata( data = lv_result ).
    RETURN.
  ENDIF.

  "调用函数发送图文消息
  CALL FUNCTION 'Z_FMINT_SEND_NEWS_TO_USER'
    EXPORTING
      iv_woid   = lv_woid
      iv_userid = lv_userid
      iv_msgid  = lv_msgid
    IMPORTING
      ev_success = lv_success
      ev_msg     = lv_msg.

  "返回结果（JSON 格式）
  IF lv_success = abap_true.
    lv_result = |{"success":true,"msg":"图文消息已发送"}|.
    "可以重定向到一个成功页面
    response->set_header_field( name = 'Location' value = |{ lv_hostname }#/success?msg=图文消息已发送| ).
    response->set_status( code = 302 ).
  ELSE.
    lv_result = |{"success":false,"msg":"{ lv_msg }"}|.
    response->set_cdata( data = lv_result ).
  ENDIF.

ENDMETHOD.


"============================================================================
"方案二：直接在发送 Markdown 后发送图文消息（简化版）
"============================================================================

"如果不需要点击触发，可以直接在发送 Markdown 消息后，同时发送图文消息

"在 send_qw.md 的第 739 行（发送 Markdown 消息之后）添加：

"发送 Markdown 消息后，同时发送图文消息给操作人
IF ls_hriskwo_h-followuserid IS NOT INITIAL.
  
  "获取用户微信 OpenID
  SELECT SINGLE wechat_openid INTO @DATA(lv_news_openid)
    FROM ztint_user_inf
    WHERE userid = @ls_hriskwo_h-followuserid
      AND wechat_openid IS NOT INITIAL.
  
  "获取微信公众号配置
  SELECT SINGLE appid appsecret INTO (@DATA(lv_news_appid), @DATA(lv_news_appsecret))
    FROM zticerp_wechat_config
    WHERE config_key = 'OFFICIAL_ACCOUNT'.
  
  IF lv_news_openid IS NOT INITIAL 
     AND lv_news_appid IS NOT INITIAL 
     AND lv_news_appsecret IS NOT INITIAL.
    
    "获取 Access Token
    DATA(lv_news_token) = zcl_wechat_official_api=>get_access_token(
      iv_appid     = lv_news_appid
      iv_appsecret = lv_news_appsecret
    ).
    
    IF lv_news_token IS NOT INITIAL.
      "构建图文消息
      DATA(lt_news_articles) = VALUE zcl_wechat_official_api=>ty_article_tab(
        ( title       = |高风险件报备 - { ls_hriskwo_h-orderid }|
          description = |订单：{ ls_hriskwo_h-orderid } | &&
                       |维修厂：{ ls_hriskwo_h-cusname } | &&
                       |零件：{ lv_partsname }|
          url         = lv_urlstring
          picurl      = 'https://img1.baidu.com/it/u=2521516110,1741003725&fm=253&fmt=auto&app=138&f=JPEG?w=800&h=500' )
        ( title       = |风险详情|
          description = |风险类型：{ COND #( WHEN lv_risktypedesc IS NOT INITIAL THEN lv_risktypedesc ELSE '安装风险' ) } | &&
                       |零件价格：{ lv_price }|
          url         = lv_urlstring )
      ).
      
      "发送图文消息
      DATA(lv_news_success) = zcl_wechat_official_api=>send_news_message(
        iv_access_token = lv_news_token
        iv_openid       = lv_news_openid
        it_articles     = lt_news_articles
      ).
      
      IF lv_news_success = abap_true.
        "记录日志：图文消息发送成功
        "可以添加到日志表
      ENDIF.
    ENDIF.
  ENDIF.
ENDIF.


"============================================================================
"方案三：使用企业微信应用发送消息（如果支持）
"============================================================================

"如果使用的是企业微信应用（不是机器人），可以通过应用消息接口发送
"但需要：
"1. 企业微信应用的 AgentID 和 Secret
"2. 用户的 userid（企业微信 userid）
"3. 企业微信应用消息接口

"示例代码结构类似，但调用的 API 不同
"需要查看企业微信官方文档：发送应用消息接口


"============================================================================
"注意事项：
"============================================================================
"
"1. URL 编码：确保回调 URL 中的参数正确编码
"
"2. 权限验证：回调接口应该验证请求来源，防止恶意调用
"   可以添加 token 或签名验证
"
"3. 用户体验：可以在回调页面显示"发送成功"的提示
"
"4. 错误处理：如果发送失败，应该记录日志并提示用户
"
"5. OpenID 获取：确保用户已经完成微信授权，获取到 OpenID
"
"============================================================================

