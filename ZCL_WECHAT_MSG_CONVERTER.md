*&---------------------------------------------------------------------*
*& 类定义：消息格式转换器
*&---------------------------------------------------------------------*
CLASS zcl_wechat_msg_converter DEFINITION
  PUBLIC
  FINAL
  CREATE PUBLIC .

  PUBLIC SECTION.
    "将 Markdown 格式转换为纯文本（用于微信）
    CLASS-METHODS markdown_to_text
      IMPORTING iv_markdown TYPE string
      RETURNING VALUE(rv_text) TYPE string.

    "清理 Markdown 格式标记
    CLASS-METHODS clean_markdown
      IMPORTING iv_content TYPE string
      RETURNING VALUE(rv_text) TYPE string.

    "提取链接文本
    CLASS-METHODS extract_link_text
      IMPORTING iv_content TYPE string
      RETURNING VALUE(rv_text) TYPE string.

  PROTECTED SECTION.
  PRIVATE SECTION.
ENDCLASS.

CLASS zcl_wechat_msg_converter IMPLEMENTATION.

  METHOD markdown_to_text.
    DATA: lv_text TYPE string.
    
    lv_text = iv_markdown.
    
    "移除字体颜色标记 <font COLOR="warning">
    REPLACE ALL OCCURRENCES OF REGEX '<font[^>]*>' IN lv_text WITH ''.
    REPLACE ALL OCCURRENCES OF '</font>' IN lv_text WITH ''.
    
    "转换 Markdown 链接格式 [文本](链接) 为 "文本：链接"
    DATA: lv_pattern TYPE string VALUE '\[([^\]]+)\]\(([^\)]+)\)',
          lv_replacement TYPE string VALUE '$1：$2'.
    
    REPLACE ALL OCCURRENCES OF REGEX lv_pattern IN lv_text WITH lv_replacement.
    
    "移除 @ 用户标记的尖括号 <@userid>，转换为 @userid
    "使用正则表达式直接替换 <@xxx> 为 @xxx
    REPLACE ALL OCCURRENCES OF REGEX '<@([^>]+)>' IN lv_text WITH '@$1'.
    
    "移除 Markdown 引用标记 >
    REPLACE ALL OCCURRENCES OF '|>' IN lv_text WITH ''.
    
    "清理多余的换行符
    REPLACE ALL OCCURRENCES OF '\n' IN lv_text WITH cl_abap_char_utilities=>newline.
    REPLACE ALL OCCURRENCES OF '\\n' IN lv_text WITH cl_abap_char_utilities=>newline.
    
    "清理多余的空格和换行
    WHILE lv_text CS cl_abap_char_utilities=>newline && cl_abap_char_utilities=>newline.
      REPLACE FIRST OCCURRENCE OF cl_abap_char_utilities=>newline && cl_abap_char_utilities=>newline 
        IN lv_text WITH cl_abap_char_utilities=>newline.
    ENDWHILE.
    
    rv_text = lv_text.
  ENDMETHOD.

  METHOD clean_markdown.
    DATA: lv_result TYPE string.
    lv_result = iv_content.
    
    "移除所有 Markdown 特殊字符
    REPLACE ALL OCCURRENCES OF '**' IN lv_result WITH ''.
    REPLACE ALL OCCURRENCES OF '*' IN lv_result WITH ''.
    REPLACE ALL OCCURRENCES OF '#' IN lv_result WITH ''.
    REPLACE ALL OCCURRENCES OF '```' IN lv_result WITH ''.
    REPLACE ALL OCCURRENCES OF '`' IN lv_result WITH ''.
    
    rv_text = lv_result.
  ENDMETHOD.

  METHOD extract_link_text.
    "提取链接中的文本部分
    "将 [文本](链接) 替换为 "文本：链接"
    DATA: lv_text TYPE string.
    
    lv_text = iv_content.
    
    "使用正则表达式替换 [文本](链接) 为 文本：链接
    "这样既保留了文本信息，也保留了链接信息
    REPLACE ALL OCCURRENCES OF REGEX '\[([^\]]+)\]\(([^\)]+)\)' IN lv_text WITH '$1：$2'.
    
    rv_text = lv_text.
  ENDMETHOD.

ENDCLASS.