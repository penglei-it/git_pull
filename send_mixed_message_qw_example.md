"============================================================================
"ABAP 示例：向企业微信发送混合消息（文字、图片、文档）
"要求：附件均为 URL 形式，图片要直接展示
"============================================================================

METHOD send_mixed_message_to_qywx.
  "**************************************************************************
  "功能说明：发送包含文字、图片、文档的混合消息到企业微信群
  "参数说明：
  "  IV_GROUP_KEY     : 企业微信机器人 Key（用于识别目标群）
  "  IV_TEXT_TITLE    : 消息标题文字
  "  IV_TEXT_CONTENT  : 消息正文内容
  "  IT_IMAGE_URLS    : 图片 URL 列表（内表，包含 URL 字符串）
  "  IT_DOC_URLS      : 文档 URL 列表（内表，包含 URL 和文件名）
  "**************************************************************************

  TYPES: BEGIN OF ty_image_url,
           url TYPE string,
         END OF ty_image_url.
  
  TYPES: BEGIN OF ty_doc_url,
           url      TYPE string,
           filename TYPE string,
         END OF ty_doc_url.

  DATA: lt_image_urls TYPE STANDARD TABLE OF ty_image_url,
        lt_doc_urls   TYPE STANDARD TABLE OF ty_doc_url,
        lv_group_key  TYPE string,
        lv_title      TYPE string,
        lv_content    TYPE string.

  "示例数据准备（实际使用时从参数传入）
  "假设传入的参数：
  lv_group_key = 'YOUR_GROUP_ROBOT_KEY'.
  lv_title = '高风险件报备'.
  lv_content = '订单存在高风险件，请及时处理'.
  
  "图片 URL 列表（示例）
  lt_image_urls = VALUE #(
    ( url = 'https://example.com/images/risk_warning.png' )
    ( url = 'https://example.com/images/parts_diagram.jpg' )
    ( url = 'https://example.com/images/installation_guide.jpeg' )
  ).
  
  "文档 URL 列表（示例）
  lt_doc_urls = VALUE #(
    ( url = 'https://example.com/docs/risk_handbook.pdf' filename = '风险处理手册.pdf' )
    ( url = 'https://example.com/docs/installation_manual.docx' filename = '安装说明文档.docx' )
    ( url = 'https://example.com/docs/parts_catalog.xlsx' filename = '零件目录表.xlsx' )
  ).

  "**************************************************************************
  "开始构建消息内容
  "**************************************************************************
  DATA: ls_sendmsg TYPE zcl_corpweixin_api=>ts_msg,
        lo_notice  TYPE REF TO zcl_cassint_notice,
        lv_index   TYPE i.

  "初始化通知对象
  lo_notice = NEW zcl_cassint_notice( ).

  "设置消息类型为 Markdown（支持富文本和图片展示）
  ls_sendmsg-msgtype = 'markdown'.

  "**************************************************************************
  "1. 构建标题和正文文字内容
  "**************************************************************************
  ls_sendmsg-content = |<font COLOR=\\"warning\\">{ lv_title }</font>  \\n  | &&
                       |  \\n  | &&
                       |>**{ lv_content }**  \\n  | &&
                       |  \\n  | &&
                       |>---  \\n  | &&
                       |  \\n  |.

  "**************************************************************************
  "2. 添加图片（直接展示，不使用引用块）
  "注意：企业微信 Markdown 支持直接显示图片，使用标准 Markdown 图片语法
  "格式：![alt文本](图片URL)
  "**************************************************************************
  IF lt_image_urls IS NOT INITIAL.
    ls_sendmsg-content = ls_sendmsg-content && |>**📷 相关图片：**  \\n  |.
    
    LOOP AT lt_image_urls INTO DATA(ls_image).
      "直接使用 Markdown 图片语法，图片会直接展示在消息中
      "不使用引用块（>），让图片以最大尺寸显示
      ls_sendmsg-content = ls_sendmsg-content &&
        |  \\n  | &&
        |![图片 { sy-tabix }]({ ls_image-url })  \\n  | &&
        |  \\n  |.
    ENDLOOP.
    
    "图片和文档之间添加分隔线
    ls_sendmsg-content = ls_sendmsg-content &&
      |  \\n  |>---  \\n  | &&
      |  \\n  |.
  ENDIF.

  "**************************************************************************
  "3. 添加文档链接（使用 Markdown 链接格式）
  "格式：[文档名称](文档URL)
  "**************************************************************************
  IF lt_doc_urls IS NOT INITIAL.
    ls_sendmsg-content = ls_sendmsg-content && |>**📄 相关文档：**  \\n  |.
    
    LOOP AT lt_doc_urls INTO DATA(ls_doc).
      "根据文件扩展名判断文档类型，添加相应图标
      DATA: lv_doc_icon TYPE string,
            lv_filename TYPE string.
      
      lv_filename = ls_doc-filename.
      TRANSLATE lv_filename TO LOWER CASE.
      
      "根据文件类型设置图标
      CASE lv_filename.
        WHEN OTHERS.
          IF lv_filename CP '*.pdf'.
            lv_doc_icon = '📕'.
          ELSEIF lv_filename CP '*.doc*'.
            lv_doc_icon = '📘'.
          ELSEIF lv_filename CP '*.xls*'.
            lv_doc_icon = '📗'.
          ELSEIF lv_filename CP '*.ppt*'.
            lv_doc_icon = '📙'.
          ELSE.
            lv_doc_icon = '📄'.
          ENDIF.
      ENDCASE.
      
      "添加文档链接（使用引用块格式，保持一致性）
      ls_sendmsg-content = ls_sendmsg-content &&
        |  \\n  | &&
        |> { lv_doc_icon } [{ ls_doc-filename }]({ ls_doc-url })  \\n  |.
    ENDLOOP.
  ENDIF.

  "**************************************************************************
  "4. 添加额外信息（示例：订单详情）
  "**************************************************************************
  DATA: lv_order_id      TYPE string VALUE 'ORD202412010001',
        lv_parts_name    TYPE string VALUE '发动机总成',
        lv_order_amount  TYPE p DECIMALS 2 VALUE '25000.00',
        lv_order_date    TYPE d VALUE '20241201',
        lv_order_time    TYPE t VALUE '143000'.

  "格式化金额
  DATA: lv_amount_str TYPE string.
  WRITE lv_order_amount TO lv_amount_str CURRENCY 'CNY'.

  "添加订单详情区域
  ls_sendmsg-content = ls_sendmsg-content &&
    |  \\n  | &&
    |>---  \\n  | &&
    |  \\n  | &&
    |>**📋 订单详情：**  \\n  | &&
    |  \\n  | &&
    |>订单号：{ lv_order_id }  \\n  | &&
    |>零件名称：{ lv_parts_name }  \\n  | &&
    |>订单金额：¥ { lv_amount_str }  \\n  | &&
    |>下单时间：{ lv_order_date DATE = ISO } { lv_order_time TIME = ISO }  \\n  | &&
    |  \\n  |.

  "**************************************************************************
  "5. 添加操作按钮（可选）
  "**************************************************************************
  DATA: lv_detail_url TYPE string.
  lv_detail_url = 'https://example.com/order/detail?id=' && lv_order_id.

  ls_sendmsg-content = ls_sendmsg-content &&
    |  \\n  | &&
    |>---  \\n  | &&
    |  \\n  | &&
    |>[🔍 查看订单详情]({ lv_detail_url })  \\n  | &&
    |>[✅ 立即处理]({ lv_detail_url })  \\n  |.

  "**************************************************************************
  "6. 发送消息到企业微信群
  "**************************************************************************
  TRY.
      lo_notice->set_robot_message(
        EXPORTING
          iv_key  = lv_group_key
          sendmsg = ls_sendmsg
      ).
      
      "发送成功后的处理（可选）
      "可以记录日志、更新状态等
      
    CATCH cx_root INTO DATA(lo_error).
      "错误处理
      DATA: lv_error_msg TYPE string.
      lv_error_msg = lo_error->get_text( ).
      
      "记录错误日志
      MESSAGE |发送企业微信消息失败: { lv_error_msg }| TYPE 'E'.
      
      "可以根据业务需要决定是否抛出异常或继续处理
      RETURN.
      
  ENDTRY.

ENDMETHOD.


"============================================================================
"使用示例（调用方法）
"============================================================================

"示例1：发送包含图片和文档的混合消息
"**************************************************************************
DATA: lt_images TYPE STANDARD TABLE OF ty_image_url,
      lt_docs   TYPE STANDARD TABLE OF ty_doc_url,
      ls_image  TYPE ty_image_url,
      ls_doc    TYPE ty_doc_url.

"准备图片 URL
ls_image-url = 'https://img.example.com/risk_image1.png'.
APPEND ls_image TO lt_images.
ls_image-url = 'https://img.example.com/risk_image2.jpg'.
APPEND ls_image TO lt_images.

"准备文档 URL
ls_doc-url = 'https://doc.example.com/manual.pdf'.
ls_doc-filename = '操作手册.pdf'.
APPEND ls_doc TO lt_docs.
ls_doc-url = 'https://doc.example.com/catalog.xlsx'.
ls_doc-filename = '零件目录.xlsx'.
APPEND ls_doc TO lt_docs.

"调用方法发送消息
CALL METHOD send_mixed_message_to_qywx
  EXPORTING
    iv_group_key   = 'YOUR_GROUP_ROBOT_KEY'
    iv_text_title  = '高风险件报备通知'
    iv_text_content = '订单 ORD202412010001 存在高风险件，请及时处理'
    it_image_urls  = lt_images
    it_doc_urls    = lt_docs.


"============================================================================
"关键说明和注意事项
"============================================================================

"1. 图片展示格式：
"   - 企业微信 Markdown 支持直接展示图片，使用语法：![描述](图片URL)
"   - 图片 URL 必须是企业微信可以访问的公网地址或内网地址
"   - 建议图片格式：PNG、JPG、JPEG、GIF、WEBP
"   - 建议图片大小：不超过 2MB

"2. 文档链接格式：
"   - 文档使用链接格式：[文档名](文档URL)
"   - 用户点击链接可以下载或在线查看文档
"   - 支持常见文档格式：PDF、DOC、DOCX、XLS、XLSX、PPT、PPTX 等

"3. 消息长度限制：
"   - 企业微信 Markdown 消息总长度建议不超过 4096 个字符
"   - 如果附件较多，建议分批发送或使用更简洁的描述

"4. URL 访问要求：
"   - 所有 URL（图片和文档）必须可以被企业微信服务器访问
"   - 如果是内网地址，需要确保企业微信可以访问
"   - 建议使用 HTTPS 协议以确保安全性

"5. 错误处理：
"   - 建议在生产环境中添加完整的异常处理和日志记录
"   - 可以添加重试机制处理临时网络问题

"6. Markdown 语法支持：
"   - 企业微信支持大部分标准 Markdown 语法
"   - 支持字体颜色（如：<font COLOR="warning">文字</font>）
"   - 支持加粗（**文字**）、斜体、代码块等
"   - 支持链接和图片嵌入

"============================================================================
"完整的方法签名（建议版本）
"============================================================================

METHOD send_mixed_message_to_qywx.
  "方法参数定义
  TYPES: BEGIN OF ty_image_url,
           url TYPE string,
         END OF ty_image_url.
  
  TYPES: BEGIN OF ty_doc_url,
           url      TYPE string,
           filename TYPE string,
         END OF ty_doc_url.

  "输入参数
  DATA: iv_group_key    TYPE string,         "企业微信机器人 Key
        iv_text_title   TYPE string,         "消息标题
        iv_text_content TYPE string,         "消息正文
        it_image_urls   TYPE STANDARD TABLE OF ty_image_url,  "图片 URL 列表
        it_doc_urls     TYPE STANDARD TABLE OF ty_doc_url.     "文档 URL 列表

  "其他业务数据（根据实际需求添加）
  DATA: iv_order_id     TYPE string,         "订单号（可选）
        iv_parts_name   TYPE string,         "零件名称（可选）
        iv_order_amount TYPE p DECIMALS 2,   "订单金额（可选）
        iv_order_date   TYPE d,              "订单日期（可选）
        iv_order_time   TYPE t,              "订单时间（可选）
        iv_detail_url   TYPE string.         "详情链接（可选）

  "方法实现...
ENDMETHOD.

