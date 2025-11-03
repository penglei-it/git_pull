*&---------------------------------------------------------------------*
*& 在 send_qw.md 中集成发送图文消息功能
*&---------------------------------------------------------------------*
*&
*& 修改位置：在发送 Markdown 消息后（第 739 行之后）
*&
*&---------------------------------------------------------------------*

"============================================================================
"修改点1：在 Markdown 消息中添加"发送图文消息"链接（第 733-735 行之后）
"============================================================================

"在原有代码：
"          IF lv_todourlstring IS NOT INITIAL.
"            ls_sendmsg-content = ls_sendmsg-content &&
"            |  \\n  >[立即处理]({ lv_todourlstring })|.
"          ENDIF.

"添加以下代码：
"添加"发送图文消息到微信"链接
DATA: lv_send_news_url TYPE string.
IF ls_hriskwo_h-followuserid IS NOT INITIAL.
  "构建发送图文消息的回调 URL
  lv_send_news_url = |{ lv_hostname }/sap/bc/webdynpro/sap/zwechat_send_news| &&
                     |?woid={ ls_hriskwo_h-woid }| &&
                     |&userid={ ls_hriskwo_h-followuserid }| &&
                     |&msgid={ lv_messageid }| &&
                     |&action=send_news|.
  
  "添加到 Markdown 消息中
  ls_sendmsg-content = ls_sendmsg-content &&
    |  \\n  >[📱 发送图文消息到微信]({ lv_send_news_url })|.
ENDIF.


"============================================================================
"修改点2：创建发送图文消息的类方法（推荐方式）
"============================================================================

*&---------------------------------------------------------------------*
*& 类方法：ZCL_CASSINT_NOTICE->SEND_NEWS_MESSAGE_TO_USER
*&---------------------------------------------------------------------*
"可以在 zcl_cassint_notice 类中添加以下方法：

CLASS zcl_cassint_notice DEFINITION.
  ...
  PUBLIC SECTION.
    ...
    "发送图文消息给用户（通过微信公众号）
    METHODS send_news_message_to_user
      IMPORTING iv_woid   TYPE ztint_hriskwo_h-woid
                iv_userid TYPE ztint_user_inf-userid
                is_detail TYPE zcl_icec_order_api=>ty_order_detail OPTIONAL
      RETURNING VALUE(rv_success) TYPE abap_bool.
  ...
ENDCLASS.


CLASS zcl_cassint_notice IMPLEMENTATION.

  METHOD send_news_message_to_user.
    DATA: ls_hriskwo_h    TYPE ztint_hriskwo_h,
          ls_user         TYPE ztint_user_inf,
          lv_appid        TYPE string,
          lv_appsecret    TYPE string,
          lv_openid       TYPE string,
          lv_access_token TYPE string,
          lt_articles     TYPE zcl_wechat_official_api=>ty_article_tab,
          ls_article      LIKE LINE OF lt_articles,
          lv_hostname     TYPE string,
          lv_detail_url   TYPE string,
          lv_image_url    TYPE string.

    "1. 获取工单信息
    SELECT SINGLE * FROM ztint_hriskwo_h INTO ls_hriskwo_h
      WHERE woid = iv_woid.
    
    IF sy-subrc <> 0.
      rv_success = abap_false.
      RETURN.
    ENDIF.

    "2. 获取用户信息和微信 OpenID
    SELECT SINGLE * FROM ztint_user_inf INTO ls_user
      WHERE userid = iv_userid.
    
    IF sy-subrc <> 0 OR ls_user-wechat_openid IS INITIAL.
      rv_success = abap_false.
      RETURN.
    ENDIF.
    lv_openid = ls_user-wechat_openid.

    "3. 获取微信公众号配置
    SELECT SINGLE appid appsecret INTO (lv_appid, lv_appsecret)
      FROM zticerp_wechat_config
      WHERE config_key = 'OFFICIAL_ACCOUNT'.
    
    IF lv_appid IS INITIAL OR lv_appsecret IS INITIAL.
      rv_success = abap_false.
      RETURN.
    ENDIF.

    "4. 获取 Access Token
    lv_access_token = zcl_wechat_official_api=>get_access_token(
      iv_appid     = lv_appid
      iv_appsecret = lv_appsecret
    ).
    
    IF lv_access_token IS INITIAL.
      rv_success = abap_false.
      RETURN.
    ENDIF.

    "5. 获取系统域名（用于构建详情链接）
    SELECT SINGLE mobileurl INTO lv_hostname
      FROM zticerp_ding_sk AS a
      INNER JOIN zticerp_ding_gid AS g ON a~gentid = g~gentid
      WHERE g~fname = 'CASSINTQYWX'.
    
    lv_detail_url = |{ COND string( WHEN lv_hostname IS NOT INITIAL THEN lv_hostname ELSE '' ) }#/newRiskSheet/detail/{ iv_woid }|.
    lv_image_url = 'https://img1.baidu.com/it/u=2521516110,1741003725&fm=253&fmt=auto&app=138&f=JPEG?w=800&h=500'.

    "6. 构建图文消息内容
    "主消息
    CLEAR ls_article.
    ls_article-title = |高风险件报备 - { ls_hriskwo_h-orderid }|.
    ls_article-description = |订单号：{ ls_hriskwo_h-orderid } | &&
                             |维修厂：{ COND string( WHEN ls_hriskwo_h-cusname IS NOT INITIAL THEN ls_hriskwo_h-cusname ELSE '' ) } | &&
                             |下单时间：{ COND string( WHEN ls_hriskwo_h-orderdate IS NOT INITIAL THEN |{ ls_hriskwo_h-orderdate DATE = ISO }| ELSE '' ) }|.
    ls_article-url = lv_detail_url.
    ls_article-picurl = lv_image_url.
    APPEND ls_article TO lt_articles.

    "零件详情消息
    IF ls_hriskwo_h-partsname IS NOT INITIAL.
      CLEAR ls_article.
      ls_article-title = |风险零件详情|.
      ls_article-description = |零件名称：{ ls_hriskwo_h-partsname } | &&
                               |车辆品牌：{ COND string( WHEN ls_hriskwo_h-carbrandname IS NOT INITIAL THEN ls_hriskwo_h-carbrandname ELSE '' ) }|.
      ls_article-url = lv_detail_url.
      APPEND ls_article TO lt_articles.
    ENDIF.

    "7. 发送图文消息
    rv_success = zcl_wechat_official_api=>send_news_message(
      iv_access_token = lv_access_token
      iv_openid       = lv_openid
      it_articles     = lt_articles
    ).

  ENDMETHOD.

ENDCLASS.


"============================================================================
"修改点3：在回调处理中使用类方法（WebDynpro 或 HTTP Service）
"============================================================================

"在 WebDynpro 组件或 HTTP Service Handler 中：

METHOD handle_callback.
  "获取 URL 参数
  DATA: lv_woid   TYPE string,
        lv_userid TYPE string,
        lv_action TYPE string,
        lv_result TYPE string.

  lv_woid   = request->get_form_field( 'woid' ).
  lv_userid = request->get_form_field( 'userid' ).
  lv_action = request->get_form_field( 'action' ).

  "验证参数
  IF lv_woid IS INITIAL OR lv_userid IS INITIAL.
    lv_result = |{"success":false,"msg":"参数不完整"}|.
    response->set_cdata( data = lv_result ).
    RETURN.
  ENDIF.

  "调用类方法发送图文消息
  DATA(lo_notice) = NEW zcl_cassint_notice( ).
  DATA(lv_success) = lo_notice->send_news_message_to_user(
    iv_woid   = lv_woid
    iv_userid = lv_userid
  ).

  "返回结果
  IF lv_success = abap_true.
    "重定向到成功页面或返回成功 JSON
    lv_result = |{"success":true,"msg":"图文消息已发送到您的微信"}|.
    "可以重定向
    response->set_header_field(
      name  = 'Location'
      value = |{ lv_hostname }#/success?msg=图文消息已发送|
    ).
    response->set_status( code = 302 ).
  ELSE.
    lv_result = |{"success":false,"msg":"发送失败，请检查微信绑定"}|.
    response->set_cdata( data = lv_result ).
  ENDIF.

ENDMETHOD.


"============================================================================
"方案对比：
"============================================================================
"
"方案一（推荐）：链接跳转触发
"  优点：
"    - 用户主动触发，体验好
"    - 可以控制发送时机
"    - 支持失败重试
"  缺点：
"    - 需要创建回调接口
"    - 需要用户点击操作
"
"方案二：自动发送
"  优点：
"    - 实现简单
"    - 无需用户操作
"  缺点：
"    - 可能发送过多消息
"    - 用户无法控制
"
"方案三：企业微信应用（如果可用）
"  优点：
"    - 在企业微信内直接显示
"    - 无需跳转
"  缺点：
"    - 需要企业微信应用权限
"    - 配置相对复杂
"
"============================================================================

