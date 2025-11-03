*&---------------------------------------------------------------------*
*& 使用现有类发送图文消息到企业微信群（推荐方案）
*&---------------------------------------------------------------------*
*&
*& 说明：
*& 1. 企业微信机器人不支持 news（图文）类型，但支持 markdown, image, text 等
*& 2. 企业微信应用消息接口支持图文消息（news），但需要应用权限
*& 3. 推荐方案：在发送 Markdown 的同时，使用企业微信应用发送图文消息
*&
*&---------------------------------------------------------------------*

"============================================================================
"方案一：发送 Markdown 后，同时发送企业微信应用图文消息到同一群（推荐）
"============================================================================

"在 send_qw.md 的第 739 行（发送 Markdown 消息之后）添加以下代码：

"发送 Markdown 消息后，同时发送企业微信图文消息到同一群
"注意：需要使用企业微信应用接口，不是机器人接口

IF lv_key IS NOT INITIAL AND lv_parts_hit IS NOT INITIAL.
  
  "获取企业微信应用配置（如果使用应用方式）
  "方式1：如果系统中有企业微信应用的类（类似 zcl_corpweixin_api）
  DATA: lo_qywx_app TYPE REF TO zcl_corpweixin_api,
        lv_qw_token TYPE string.
  
  "创建企业微信应用实例（用于发送应用消息）
  lo_qywx_app = NEW zcl_corpweixin_api( 
    appname = 'CASSINTQYWX' 
    destination = 'QYWX' 
  ).
  
  "获取企业微信应用 Access Token
  DO 5 TIMES.
    lv_qw_token = lo_qywx_app->get_corpwx_token( ).
    IF lv_qw_token IS NOT INITIAL.
      EXIT.
    ENDIF.
    IF sy-index < 5.
      WAIT UP TO 1 SECONDS.
    ENDIF.
  ENDDO.
  
  IF lv_qw_token IS NOT INITIAL.
    "构建企业微信图文消息（news 类型）
    "注意：企业微信的图文消息格式与微信公众号略有不同
    DATA: lv_news_body TYPE string,
          lv_articles_json TYPE string VALUE '',
          lv_first_article TYPE abap_bool VALUE abap_true,
          lv_chatid TYPE string.
    
    "获取群聊 ID（从 lv_key 或其他方式获取）
    "如果 lv_key 就是 chatid，可以直接使用
    "否则需要根据群机器人 key 查找对应的 chatid
    lv_chatid = lv_key.  "根据实际情况调整
    
    "构建第一条图文消息
    DATA(ls_article1) = VALUE string(
      |\{| &&
      |"title":"高风险件报备 - { ls_hriskwo_h-orderid }",| &&
      |"description":"订单号：{ ls_hriskwo_h-orderid } \\n| &&
      |维修厂：{ ls_hriskwo_h-cusname } \\n| &&
      |零件：{ lv_partsname }",| &&
      |"url":"{ lv_urlstring }",| &&
      |"picurl":"{ lv_url1 }"| &&
      |\}|
    ).
    
    "如果有更多信息，添加第二条图文消息
    DATA(ls_article2) = VALUE string(
      |\{| &&
      |"title":"风险详情",| &&
      |"description":"风险类型：{ COND #( WHEN lv_risktypedesc IS NOT INITIAL THEN lv_risktypedesc ELSE '安装风险' ) } \\n| &&
      |零件价格：{ lv_price }",| &&
      |"url":"{ lv_urlstring }"| &&
      |\}|
    ).
    
    lv_articles_json = |[{ ls_article1 },{ ls_article2 }]|.
    
    "构建企业微信图文消息请求体
    "企业微信应用发送图文消息到群聊的 API
    "参考：https://developer.work.weixin.qq.com/document/path/90248
    lv_news_body = |\{| &&
                  |"chatid":"{ lv_chatid }",| &&
                  |"msgtype":"news",| &&
                  |"news":\{| &&
                  |  "articles":{ lv_articles_json }| &&
                  |\}| &&
                  |\}|.
    
    "发送企业微信应用消息（需要使用企业微信应用接口）
    "注意：需要实现企业微信应用发送消息的方法
    "可以调用类似的方法或直接调用企业微信 API
    
    "方式A：如果 zcl_corpweixin_api 有发送应用消息的方法
    "DATA(lv_success) = lo_qywx_app->send_app_message(
    "  iv_token = lv_qw_token
    "  iv_chatid = lv_chatid
    "  iv_body = lv_news_body
    ").
    
    "方式B：直接调用企业微信 API（参考现有的实现方式）
    DATA(lo_api) = NEW zcl_icec_api( ).
    DATA: lv_qw_api_url TYPE string,
          ls_content_type TYPE zapi_s_contenttype.
    
    lv_qw_api_url = |/cgi-bin/appchat/send?access_token={ lv_qw_token }|.
    ls_content_type-content_type = 'application/json;charset=UTF-8'.
    
    "调用企业微信应用消息接口
    TRY.
        CALL METHOD lo_api->post_data(
          EXPORTING
            iv_rfcdest      = 'QYWX'
            iv_uri          = lv_qw_api_url
            is_content_type = ls_content_type
            iv_body         = lv_news_body
          IMPORTING
            json_out        = DATA(lv_qw_response)
            ev_msg          = DATA(ls_qw_msg)
        ).
        
        "检查是否成功
        IF lv_qw_response CS '"errcode":0'.
          "发送成功
          "可以记录日志
        ELSE.
          "发送失败，记录日志但不影响主流程
          MESSAGE |企业微信图文消息发送失败: { lv_qw_response }| TYPE 'W'.
        ENDIF.
      CATCH cx_root INTO DATA(lo_qw_error).
        "异常处理
        MESSAGE |发送企业微信图文消息异常: { lo_qw_error->get_text( ) }| TYPE 'W'.
    ENDTRY.
  ENDIF.
ENDIF.


"============================================================================
"方案二：使用 Markdown 增强显示（更简单，推荐用于企业微信群）
"============================================================================

"企业微信机器人支持 Markdown，可以在 Markdown 中添加图片链接实现类似图文效果

"在原有 Markdown 消息基础上增强（send_qw.md 第 700-714 行之间添加）：

"添加图片和链接，实现类似图文消息的效果
IF lv_url1 IS NOT INITIAL.
  "在 Markdown 中添加图片（企业微信支持）
  ls_sendmsg-content = ls_sendmsg-content &&
    |  \\n  >![风险提示图]({ lv_url1 }) \\n |.
ENDIF.

"添加更多结构化内容（使用 Markdown 格式）
ls_sendmsg-content = ls_sendmsg-content &&
  |  \\n  >--- \\n | &&
  |  \\n  ><font COLOR=\\"info\\">📋 详细信息</font> \\n | &&
  |  \\n  >**订单号：** [{ ls_hriskwo_h-orderid }]({ lv_urlstring }) \\n | &&
  |  \\n  >**零件号：** { lv_partsno } \\n | &&
  |  \\n  >**零件名称：** { lv_partsname } \\n | &&
  |  \\n  >**零件价格：** ¥{ lv_price } \\n | &&
  |  \\n  >**车辆品牌：** { lv_carbrandname } \\n | &&
  |  \\n  >**品质：** { lv_qualityname } \\n |;

"这样可以在同一个群中显示类似图文消息的效果


"============================================================================
"方案三：使用企业微信卡片消息（action_card）
"============================================================================

"企业微信支持卡片消息，可以在 Markdown 后发送卡片消息到同一群

"在发送 Markdown 之后添加：

IF lv_key IS NOT INITIAL.
  "构建企业微信卡片消息
  DATA: ls_card_msg TYPE zcl_corpweixin_api=>ts_msg.
  
  ls_card_msg-msgtype = 'template_card'.
  
  "构建卡片消息内容（企业微信模板卡片格式）
  "注意：需要根据企业微信实际支持的消息类型调整
  ls_card_msg-content = |\{| &&
    |"card_type":"text_notice",| &&
    |"source":\{| &&
    |  "icon_url":"{ lv_url1 }",| &&
    |  "desc":"高风险件报备",| &&
    |  "desc_color":1| &&
    |\},| &&
    |"main_title":\{| &&
    |  "title":"高风险件报备 - { ls_hriskwo_h-orderid }",| &&
    |  "desc":"订单号：{ ls_hriskwo_h-orderid }"| &&
    |\},| &&
    |"emphasis_content":\{| &&
    |  "title":"风险提示",| &&
    |  "desc":"请及时处理"| &&
    |\},| &&
    |"horizontal_content_list":[\{| &&
    |  "keyname":"零件名称",| &&
    |  "value":"{ lv_partsname }"| &&
    |\},\{| &&
    |  "keyname":"零件价格",| &&
    |  "value":"¥{ lv_price }"| &&
    |\}],| &&
    |"jump_list":[\{| &&
    |  "type":1,| &&
    |  "title":"查看详情",| &&
    |  "url":"{ lv_urlstring }"| &&
    |\}],| &&
    |"card_action":\{| &&
    |  "type":1,| &&
    |  "url":"{ lv_urlstring }"| &&
    |\}| &&
    |\}|.
  
  "发送卡片消息到同一群
  lo_notice->set_robot_message(
    EXPORTING
      iv_key  = lv_key
      sendmsg = ls_card_msg
  ).
ENDIF.


"============================================================================
"方案四：发送多条消息组合（Markdown + 图片）
"============================================================================

"发送 Markdown 后，再发送图片消息到同一群

"在原有 Markdown 消息发送之后（第 739 行之后）：

"发送图片消息到同一群
IF lv_key IS NOT INITIAL AND lv_url1 IS NOT INITIAL.
  CLEAR ls_sendmsg.
  ls_sendmsg-msgtype = 'image'.
  
  "企业微信图片消息格式
  "注意：需要先上传图片获取 media_id，或使用图片 URL
  "如果支持直接使用 URL
  ls_sendmsg-content = |\{| &&
                      |"base64":"",| &&
                      |"md5":"",| &&
                      |"pic_url":"{ lv_url1 }"| &&
                      |\}|.
  
  "发送图片消息
  lo_notice->set_robot_message(
    EXPORTING
      iv_key  = lv_key
      sendmsg = ls_sendmsg
  ).
ENDIF.


"============================================================================
"推荐方案对比：
"============================================================================
"
"方案一（企业微信应用图文消息）：
"   优点：真正的图文消息，格式美观
"   缺点：需要应用权限，配置相对复杂
"
"方案二（Markdown 增强）：
"   优点：简单易用，使用现有机器人即可
"   缺点：不是真正的图文消息，只是增强的 Markdown
"
"方案三（卡片消息）：
"   优点：企业微信原生支持，格式美观
"   缺点：需要确认企业微信机器人是否支持 template_card
"
"方案四（Markdown + 图片）：
"   优点：实现简单
"   缺点：分成两条消息，可能被打断
"
"============================================================================
"建议：优先使用方案二（Markdown 增强），如果必须使用图文消息，使用方案一
"============================================================================

