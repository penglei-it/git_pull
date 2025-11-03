*&---------------------------------------------------------------------*
*& 直接在 send_qw.md 中发送企业微信图文消息（推荐集成代码）
*&---------------------------------------------------------------------*
*&
*& 说明：在发送 Markdown 消息后，直接发送企业微信图文消息到同一群
*& 使用现有的企业微信 API 类
*&
*&---------------------------------------------------------------------*

"============================================================================
"集成位置：在 send_qw.md 第 739 行（发送 Markdown 消息之后）添加
"============================================================================

"在原有代码：
"          lo_notice->set_robot_message(
"          EXPORTING
"            iv_key  = lv_key
"            sendmsg = ls_sendmsg ).
"
"之后添加以下代码：

"============================================================================
"方案：使用企业微信应用接口发送图文消息到同一群
"============================================================================

"在发送 Markdown 消息后，发送图文消息到同一群
IF lv_key IS NOT INITIAL AND lv_parts_hit IS NOT INITIAL.
  
  "方式A：如果 zcl_corpweixin_api 支持发送应用消息
  "创建企业微信应用实例
  DATA(lo_qywx) = NEW zcl_corpweixin_api( 
    appname = 'CASSINTQYWX' 
    destination = 'QYWX' 
  ).
  
  "获取企业微信 Token
  DATA: lv_qw_token TYPE string.
  DO 5 TIMES.
    lv_qw_token = lo_qywx->get_corpwx_token( ).
    IF lv_qw_token IS NOT INITIAL.
      EXIT.
    ENDIF.
    IF sy-index < 5.
      WAIT UP TO 1 SECONDS.
    ENDIF.
  ENDDO.
  
  IF lv_qw_token IS NOT INITIAL.
    "构建企业微信图文消息
    DATA: lv_news_body TYPE string,
          lv_article1  TYPE string,
          lv_article2  TYPE string.
    
    "第一条图文消息
    lv_article1 = |\{| &&
                  |"title":"高风险件报备 - { ls_hriskwo_h-orderid }",| &&
                  |"description":"订单号：{ ls_hriskwo_h-orderid } \\n| &&
                  |维修厂：{ ls_hriskwo_h-cusname } \\n| &&
                  |零件：{ lv_partsname }",| &&
                  |"url":"{ lv_urlstring }",| &&
                  |"picurl":"{ lv_url1 }"| &&
                  |\}|.
    
    "第二条图文消息（风险详情）
    IF lv_risktypedesc IS NOT INITIAL OR lv_price IS NOT INITIAL.
      lv_article2 = |\{| &&
                    |"title":"风险详情",| &&
                    |"description":"风险类型：{ COND #( WHEN lv_risktypedesc IS NOT INITIAL THEN lv_risktypedesc ELSE '安装风险' ) } \\n| &&
                    |零件价格：{ lv_price }",| &&
                    |"url":"{ lv_urlstring }"| &&
                    |\}|
                    && |,{ lv_article1 }|.  "将第一条消息追加
      lv_article1 = lv_article2.
    ENDIF.
    
    lv_news_body = |\{| &&
                  |"chatid":"{ lv_key }",| &&
                  |"msgtype":"news",| &&
                  |"news":\{| &&
                  |  "articles":[{ lv_article1 }]| &&
                  |\}| &&
                  |\}|.
    
    "调用企业微信应用消息接口发送图文消息
    DATA(lo_api) = NEW zcl_icec_api( ).
    DATA: lv_qw_uri TYPE string VALUE '/cgi-bin/appchat/send?access_token={ACCESS_TOKEN}',
          ls_content_type TYPE zapi_s_contenttype.
    
    REPLACE '{ACCESS_TOKEN}' IN lv_qw_uri WITH lv_qw_token.
    ls_content_type-content_type = 'application/json;charset=UTF-8'.
    
    TRY.
        CALL METHOD lo_api->post_data(
          EXPORTING
            iv_rfcdest      = 'QYWX'
            iv_uri          = lv_qw_uri
            is_content_type = ls_content_type
            iv_body         = lv_news_body
          IMPORTING
            json_out        = DATA(lv_qw_result)
            ev_msg          = DATA(ls_qw_msg)
        ).
        
        "检查结果
        IF lv_qw_result CS '"errcode":0'.
          "发送成功，可以记录日志
        ELSE.
          "发送失败，记录日志但不影响主流程
          "MESSAGE |企业微信图文消息发送失败: { lv_qw_result }| TYPE 'W'.
        ENDIF.
      CATCH cx_root INTO DATA(lo_qw_error).
        "异常处理，不影响主流程
        "MESSAGE |发送企业微信图文消息异常: { lo_qw_error->get_text( ) }| TYPE 'W'.
    ENDTRY.
  ENDIF.
ENDIF.


"============================================================================
"简化方案：使用 Markdown 增强显示（无需额外 API 调用，推荐）
"============================================================================

"如果不需要真正的图文消息，可以在原有 Markdown 消息中增强显示效果
"这样不需要额外的 API 调用，直接使用机器人消息

"在构建 ls_sendmsg-content 时（第 700 行左右），可以添加：

"在原有 Markdown 内容基础上，添加图片和更好的格式
IF lv_url1 IS NOT INITIAL.
  "添加图片（企业微信 Markdown 支持图片链接）
  ls_sendmsg-content = ls_sendmsg-content &&
    |  \\n  >![风险提示]({ lv_url1 }) \\n |.
ENDIF.

"添加分隔线和详细信息区域（增强 Markdown 显示效果）
ls_sendmsg-content = ls_sendmsg-content &&
  |  \\n  >--- \\n | &&
  |  \\n  ><font COLOR=\\"info\\">📋 详细信息</font> \\n | &&
  |  \\n  >**订单号：** [{ ls_hriskwo_h-orderid }]({ lv_urlstring }) \\n | &&
  |  \\n  >**零件号：** { lv_partsno } \\n | &&
  |  \\n  >**零件名称：** { lv_partsname } \\n | &&
  |  \\n  >**零件价格：** ¥{ lv_price } \\n | &&
  |  \\n  >**车辆品牌：** { lv_carbrandname } \\n | &&
  |  \\n  >**品质：** { lv_qualityname } \\n | &&
  |  \\n  >**下单时间：** { ls_hriskwo_h-orderdate DATE = ISO } { ls_hriskwo_h-ordertime TIME = ISO } \\n |;

"这样可以在一个 Markdown 消息中实现类似图文消息的显示效果
"而且不需要额外的 API 调用，更加简单高效


"============================================================================
"注意事项：
"============================================================================
"
"1. 企业微信机器人消息类型：
"   - 支持：text, markdown, image, file
"   - 不支持：news（图文消息）
"
"2. 企业微信应用消息类型：
"   - 支持：text, image, video, file, news（图文）
"   - 需要应用权限和 chatid（群聊 ID）
"
"3. 推荐方案：
"   - 如果只是要更好的显示效果：使用 Markdown 增强（方案二）
"   - 如果必须使用真正的图文消息：使用应用消息接口（方案一）
"
"4. chatid 获取：
"   - 如果 lv_key 是群聊 ID，直接使用
"   - 否则需要通过群机器人 key 查找对应的 chatid
"
"============================================================================

