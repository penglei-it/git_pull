*&---------------------------------------------------------------------*
*& 类定义：微信公众号 API 服务
*&---------------------------------------------------------------------*
CLASS zcl_wechat_official_api DEFINITION
  PUBLIC
  FINAL
  CREATE PUBLIC .

  PUBLIC SECTION.
    "类型定义
    TYPES: BEGIN OF ty_access_token,
             access_token TYPE string,
             expires_in   TYPE i,
             expires_at   TYPE timestamp,
           END OF ty_access_token.

    TYPES: BEGIN OF ty_send_result,
             openid  TYPE string,
             success TYPE abap_bool,
             error   TYPE string,
           END OF ty_send_result,
           ty_send_result_tab TYPE TABLE OF ty_send_result WITH DEFAULT KEY.

    TYPES: BEGIN OF ty_article,
             title       TYPE string,
             description TYPE string,
             url         TYPE string,
             picurl      TYPE string,
           END OF ty_article,
           ty_article_tab TYPE TABLE OF ty_article WITH DEFAULT KEY.

    "实例变量（参考 ZCL_CORPWEIXIN_APPROVAL_API 的设计）
    DATA: mv_appid      TYPE string READ-ONLY,
          mv_appsecret   TYPE string READ-ONLY,
          mv_destination TYPE RFCDES-RFCDEST READ-ONLY.

    "构造函数（参考 ZCL_CORPWEIXIN_APPROVAL_API）
    METHODS constructor
      IMPORTING iv_appid      TYPE string OPTIONAL
                iv_appsecret  TYPE string OPTIONAL
                iv_destination TYPE RFCDES-RFCDEST OPTIONAL.

    "获取微信公众号 Access Token（实例方法，参考 get_corpwx_token）
    "如果已通过构造函数设置了 appid 和 appsecret，则使用它们
    METHODS get_wechat_token
      RETURNING VALUE(rv_token) TYPE string.

    "获取 Access Token（静态方法，保持向后兼容）
    CLASS-METHODS get_access_token
      IMPORTING iv_appid     TYPE string
                iv_appsecret TYPE string
      RETURNING VALUE(rv_token) TYPE string.

    "发送文本消息（异常在内部处理，返回 abap_false 表示失败）
    CLASS-METHODS send_text_message
      IMPORTING iv_access_token TYPE string
                iv_openid        TYPE string
                iv_content       TYPE string
      RETURNING VALUE(rv_success) TYPE abap_bool.

    "批量发送文本消息
    CLASS-METHODS batch_send_text
      IMPORTING iv_access_token TYPE string
                it_openids      TYPE string_table
                iv_content       TYPE string
      RETURNING VALUE(rt_results) TYPE ty_send_result_tab.

    "发送图文消息（异常在内部处理，返回 abap_false 表示失败）
    CLASS-METHODS send_news_message
      IMPORTING iv_access_token TYPE string
                iv_openid        TYPE string
                it_articles      TYPE ty_article_tab
      RETURNING VALUE(rv_success) TYPE abap_bool.

    "获取 OAuth 授权 URL（用于获取用户 OpenID）
    CLASS-METHODS get_oauth_url
      IMPORTING iv_appid        TYPE string
                iv_redirect_uri TYPE string
                iv_state        TYPE string OPTIONAL
                iv_scope        TYPE string DEFAULT 'snsapi_base'
      RETURNING VALUE(rv_url)   TYPE string.

    "通过 OAuth code 获取用户 OpenID
    CLASS-METHODS get_openid_by_code
      IMPORTING iv_appid     TYPE string
                iv_appsecret TYPE string
                iv_code      TYPE string
      RETURNING VALUE(rv_openid) TYPE string.

  PROTECTED SECTION.
    CLASS-DATA: gv_token_cache TYPE ty_access_token.
    DATA: mv_token_cache TYPE ty_access_token.

    CLASS-METHODS call_api
      IMPORTING iv_url         TYPE string
                iv_method      TYPE string DEFAULT 'GET'
                iv_body        TYPE string OPTIONAL
                iv_destination TYPE RFCDES-RFCDEST OPTIONAL
      RETURNING VALUE(rv_json) TYPE string.

    METHODS call_api_instance
      IMPORTING iv_url         TYPE string
                iv_method      TYPE string DEFAULT 'GET'
                iv_body        TYPE string OPTIONAL
      RETURNING VALUE(rv_json) TYPE string.

  PRIVATE SECTION.
ENDCLASS.



CLASS ZCL_WECHAT_OFFICIAL_API IMPLEMENTATION.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_WECHAT_OFFICIAL_API=>CONSTRUCTOR
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_APPID                         TYPE        STRING (optional)
* | [--->] IV_APPSECRET                     TYPE        STRING (optional)
* | [--->] IV_DESTINATION                   TYPE        RFCDES-RFCDEST (optional)
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD constructor.
    "参考 ZCL_CORPWEIXIN_APPROVAL_API 的 CONSTRUCTOR 实现
    
    "如果提供了 appid 和 appsecret，直接使用
    IF iv_appid IS NOT INITIAL AND iv_appsecret IS NOT INITIAL.
      mv_appid = iv_appid.
      mv_appsecret = iv_appsecret.
    ENDIF.
    "注意：如果未提供参数，mv_appid 和 mv_appsecret 将保持为空
    "建议：在调用时直接传入参数，或在 get_wechat_token 中从配置源读取
    "这样可以避免依赖特定的配置表

    "设置 destination（如果提供）
    IF iv_destination IS NOT INITIAL.
      mv_destination = iv_destination.
    ELSE.
      "默认 destination
      mv_destination = 'WECHAT_OFFICIAL_API'.
    ENDIF.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_WECHAT_OFFICIAL_API=>GET_WECHAT_TOKEN
* +-------------------------------------------------------------------------------------------------+
* | [<-()] RV_TOKEN                         TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD get_wechat_token.
    "参考 ZCL_CORPWEIXIN_APPROVAL_API 中 get_corpwx_token 的使用方式
    "如果已设置 appid 和 appsecret，使用它们获取 token
    
    "检查是否已通过构造函数设置了配置
    IF mv_appid IS INITIAL OR mv_appsecret IS INITIAL.
      "如果未设置，提示需要传入参数或从配置源读取
      "注意：不依赖特定的配置表，调用者可以在构造函数中传入参数
      "或者在此处添加从实际配置表读取的逻辑（根据实际系统调整）
      "例如：
      "   SELECT SINGLE value1 value2 INTO (mv_appid, mv_appsecret)
      "     FROM ztint_par
      "     WHERE fname = 'WECHAT_APPID'.
      "
      "或者从其他配置机制读取
      
      MESSAGE '未设置微信公众号 AppID 和 AppSecret，请在构造函数中传入参数' TYPE 'W'.
      rv_token = ''.
      RETURN.
    ENDIF.

    IF mv_appid IS NOT INITIAL AND mv_appsecret IS NOT INITIAL.
      "使用实例方法获取 token（支持重试机制，参考 get_corpwx_token）
      DATA: lv_current_time TYPE timestamp.
      GET TIME STAMP FIELD lv_current_time.

      "检查实例缓存
      IF mv_token_cache-expires_at > lv_current_time.
        rv_token = mv_token_cache-access_token.
        RETURN.
      ENDIF.

      "尝试获取 token，最多重试 5 次（参考 get_corpwx_token 的实现）
      DO 5 TIMES.
        rv_token = get_access_token(
          iv_appid     = mv_appid
          iv_appsecret = mv_appsecret
        ).
        
        IF rv_token IS NOT INITIAL.
          "缓存 token（使用实例缓存）
          mv_token_cache-access_token = rv_token.
          "从类缓存中获取过期时间（因为 get_access_token 更新了类缓存）
          mv_token_cache-expires_at = gv_token_cache-expires_at.
          EXIT.
        ENDIF.
        
        "如果失败，等待后重试
        IF sy-index < 5.
          WAIT UP TO 1 SECONDS.
        ENDIF.
      ENDDO.
    ELSE.
      rv_token = ''.
      MESSAGE '未配置微信公众号 AppID 和 AppSecret' TYPE 'W'.
    ENDIF.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Static Public Method ZCL_WECHAT_OFFICIAL_API=>BATCH_SEND_TEXT
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_ACCESS_TOKEN                TYPE        STRING
* | [--->] IT_OPENIDS                     TYPE        STRING_TABLE
* | [--->] IV_CONTENT                     TYPE        STRING
* | [<-()] RT_RESULTS                     TYPE        TY_SEND_RESULT_TAB
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD batch_send_text.
    DATA: ls_result TYPE ty_send_result,
          lv_success TYPE abap_bool.

    LOOP AT it_openids INTO DATA(lv_openid).
      CLEAR: ls_result.
      ls_result-openid = lv_openid.

      "发送消息（异常已在方法内部处理）
      lv_success = send_text_message(
        iv_access_token = iv_access_token
        iv_openid       = lv_openid
        iv_content      = iv_content
      ).

      IF lv_success = abap_true.
        ls_result-success = abap_true.
      ELSE.
        ls_result-success = abap_false.
        ls_result-error = '发送失败'.
      ENDIF.

      APPEND ls_result TO rt_results.

      "避免频率限制，延迟 100ms
      WAIT UP TO 1 SECONDS.
    ENDLOOP.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Static Protected Method ZCL_WECHAT_OFFICIAL_API=>CALL_API
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_URL                         TYPE        STRING
* | [--->] IV_METHOD                      TYPE        STRING (default ='GET')
* | [--->] IV_BODY                        TYPE        STRING(optional)
* | [<-()] RV_JSON                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD call_api.
    DATA: lo_http_client TYPE REF TO if_http_client,
          lv_host        TYPE string,
          lv_path        TYPE string,
          lv_status      TYPE i,
          lv_url         TYPE string,
          lv_protocol    TYPE string,
          lv_host_path   TYPE string.

    lv_url = iv_url.

    "解析 URL（提取协议、主机和路径）
    "格式：https://api.weixin.qq.com/cgi-bin/token?...
    FIND REGEX '^([^:]+)://([^/]+)(.*)$' IN lv_url
      SUBMATCHES lv_protocol lv_host lv_path.

    IF lv_path IS INITIAL.
      lv_path = '/'.
    ENDIF.

    DATA: lv_port TYPE string VALUE 443.
    DATA: lv_dest_exists TYPE abap_bool VALUE abap_false.
    DATA: lv_destination TYPE RFCDES-RFCDEST.

    "确定要使用的 destination
    IF iv_destination IS NOT INITIAL.
      lv_destination = iv_destination.
    ELSE.
      "默认 destination（如果未提供）
      lv_destination = 'WECHAT_OFFICIAL_API'.
    ENDIF.

    TRY.
        "创建 HTTP 客户端
        "方案1：尝试使用 HTTP 目标配置（推荐，在 SM59 中配置）
        "注意：如果系统不支持，会抛出异常，自动转到方案2
        CALL METHOD cl_http_client=>create_by_destination
          EXPORTING
            destination = lv_destination
          IMPORTING
            client      = lo_http_client
          EXCEPTIONS
            argument_not_found      = 1
            destination_not_found   = 2
            destination_no_authority = 3
*            plugin_not_available    = 4
            internal_error          = 5
            OTHERS                  = 6.

        IF sy-subrc = 0 AND lo_http_client IS BOUND.
          lv_dest_exists = abap_true.
        ELSE.
          lv_dest_exists = abap_false.
        ENDIF.

      CATCH cx_root.
        "方案1失败，继续尝试方案2
        lv_dest_exists = abap_false.
    ENDTRY.

    "如果方案1失败，使用方案2：直接使用主机和端口创建
    IF lv_dest_exists = abap_false.
      TRY.
          CALL METHOD cl_http_client=>create
            EXPORTING
              host    = lv_host
              service = lv_port
              scheme  = 1  "0=HTTP, 1=HTTPS
            IMPORTING
              client  = lo_http_client
            EXCEPTIONS
              argument_not_found = 1
*              plugin_not_available = 2
              internal_error     = 3
              OTHERS             = 4.

          IF lo_http_client IS NOT BOUND.
            MESSAGE '创建 HTTP 客户端失败，请检查 SM59 配置或网络连接' TYPE 'W'.
            rv_json = ''.
            RETURN.
          ENDIF.

        CATCH cx_root INTO DATA(lo_create_error).
          MESSAGE |创建 HTTP 客户端失败: { lo_create_error->get_text( ) }| TYPE 'W'.
          rv_json = ''.
          RETURN.
      ENDTRY.
    ENDIF.

    "检查客户端是否已成功创建
    IF lo_http_client IS NOT BOUND.
      MESSAGE 'HTTP 客户端未创建，无法发送请求' TYPE 'W'.
      rv_json = ''.
      RETURN.
    ENDIF.

    "HTTP 请求处理逻辑（放在 TRY 块中以处理可能的异常）
    TRY.
        "设置请求方法
        IF iv_method = 'GET'.
          CALL METHOD lo_http_client->request->set_method
            EXPORTING
              method = 'GET'.
        ELSEIF iv_method = 'POST'.
          CALL METHOD lo_http_client->request->set_method
            EXPORTING
              method = 'POST'.
        ENDIF.

        "设置请求 URI
        "提取完整路径（包括查询参数，如 /cgi-bin/token?grant_type=...）
        DATA: lv_request_uri TYPE string.
        
        "提取 URL 中的路径部分（包括查询参数）
        FIND REGEX '^[^:]+://[^/]+(.*)$' IN iv_url
          SUBMATCHES lv_request_uri.
        
        IF lv_request_uri IS INITIAL.
          lv_request_uri = lv_path.
        ENDIF.

        "设置请求 URI
        "参考 ZCL_CORPWEIXIN_APPROVAL_API 的实现方式
        "无论是通过 destination 还是直接创建，都需要设置 URI 路径
        TRY.
            "尝试使用标准方法设置 URI（适用于大多数 SAP 版本）
            "某些版本支持通过 header 字段设置 URI
            CALL METHOD lo_http_client->request->set_header_field
              EXPORTING
                name  = '~request_uri'
                value = lv_request_uri.
          CATCH cx_root INTO DATA(lo_uri_error).
            "如果设置 URI 失败，记录警告
            "如果使用 create_by_destination，确保在 SM59 中配置了正确的路径前缀
            MESSAGE |无法设置请求 URI: { lo_uri_error->get_text( ) }，请检查 SM59 配置| TYPE 'W'.
        ENDTRY.

        "设置请求头
        IF iv_method = 'POST'.
          CALL METHOD lo_http_client->request->set_header_field
            EXPORTING
              name  = 'Content-Type'
              value = 'application/json; charset=utf-8'.
        ENDIF.

        "设置请求体（POST 请求）
        IF iv_method = 'POST' AND iv_body IS NOT INITIAL.
          CALL METHOD lo_http_client->request->set_cdata( data = iv_body ).
        ENDIF.

        "发送请求
        CALL METHOD lo_http_client->send
          EXCEPTIONS
            http_communication_failure = 1
            http_invalid_state         = 2
            http_processing_failed      = 3
            http_invalid_timeout        = 4
            OTHERS                     = 5.

        IF sy-subrc <> 0.
          MESSAGE 'HTTP 请求发送失败' TYPE 'W'.
          rv_json = ''.
          RETURN.
        ENDIF.

        "接收响应
        CALL METHOD lo_http_client->receive
          EXCEPTIONS
            http_communication_failure = 1
            http_invalid_state         = 2
            http_processing_failed     = 3
            OTHERS                     = 4.

        IF sy-subrc <> 0.
          MESSAGE 'HTTP 响应接收失败' TYPE 'W'.
          rv_json = ''.
          RETURN.
        ENDIF.

        "获取状态码
        CALL METHOD lo_http_client->response->get_status
          IMPORTING
            code   = lv_status.

        "获取响应内容
        CALL METHOD lo_http_client->response->get_cdata
          RECEIVING
            data = rv_json.

        "关闭客户端
        CALL METHOD lo_http_client->close
          EXCEPTIONS
            http_invalid_state = 1
            OTHERS             = 2.

      CATCH cx_root INTO DATA(lo_error).
        "关闭客户端（如果已创建）
        IF lo_http_client IS BOUND.
          TRY.
              CALL METHOD lo_http_client->close.
            CATCH cx_root.
              "忽略关闭时的错误
          ENDTRY.
        ENDIF.
        "发生异常时记录错误并返回空字符串
        MESSAGE |HTTP 请求失败: { lo_error->get_text( ) }| TYPE 'W'.
        rv_json = ''.
        RETURN.
    ENDTRY.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Static Public Method ZCL_WECHAT_OFFICIAL_API=>GET_ACCESS_TOKEN
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_APPID                       TYPE        STRING
* | [--->] IV_APPSECRET                   TYPE        STRING
* | [<-()] RV_TOKEN                       TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD get_access_token.
    DATA: lv_url     TYPE string,
          lv_json    TYPE string,
          lo_json    TYPE REF TO cl_trex_json_serializer,
          lv_result  TYPE string,
          lv_expires TYPE timestamp,
          lv_current_time TYPE timestamp.

    "获取当前系统时间
    GET TIME STAMP FIELD lv_current_time.

    "检查缓存
    IF gv_token_cache-expires_at > lv_current_time.
      rv_token = gv_token_cache-access_token.
      RETURN.
    ENDIF.

    "构建请求 URL
    lv_url = |https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid={ iv_appid }&secret={ iv_appsecret }|.

    "调用 API（使用 TRY-CATCH 处理异常）
    TRY.
        lv_json = call_api( iv_url = lv_url ).
      CATCH cx_root INTO DATA(lo_error).
        "发生异常时记录错误并返回空
        MESSAGE |获取 Access Token 失败: { lo_error->get_text( ) }| TYPE 'W'.
        rv_token = ''.
        RETURN.
    ENDTRY.

    "检查响应是否为空
    IF lv_json IS INITIAL.
      MESSAGE '获取 Access Token 失败：响应为空' TYPE 'W'.
      rv_token = ''.
      RETURN.
    ENDIF.

    "解析 JSON 响应（使用字符串查找方法，简单可靠）
    "格式：{"access_token":"xxx","expires_in":7200}
    FIND FIRST OCCURRENCE OF '"access_token":"' IN lv_json
      MATCH OFFSET DATA(lv_offset).
    IF sy-subrc = 0.
      DATA(lv_start) = lv_offset + 18.
      FIND FIRST OCCURRENCE OF '"' IN SECTION OFFSET lv_start OF lv_json
        MATCH OFFSET DATA(lv_end).
      IF sy-subrc = 0.
        DATA(lv_len) = lv_end - lv_start.
        rv_token = lv_json+lv_start(lv_len).

        "缓存 token
        gv_token_cache-access_token = rv_token.
        FIND FIRST OCCURRENCE OF '"expires_in":' IN lv_json
          MATCH OFFSET lv_offset.
        IF sy-subrc = 0.
          lv_start = lv_offset + 12.
          FIND FIRST OCCURRENCE OF '}' IN SECTION OFFSET lv_start OF lv_json
            MATCH OFFSET lv_end.
          IF sy-subrc = 0.
            lv_len = lv_end - lv_start.
            DATA(lv_expires_in) = lv_json+lv_start(lv_len).
            "计算过期时间（提前 5 分钟）
            GET TIME STAMP FIELD lv_expires.
            lv_expires = cl_abap_tstmp=>add( tstmp = lv_expires
                                              secs = CONV #( lv_expires_in - 300 ) ).
            gv_token_cache-expires_at = lv_expires.
          ENDIF.
        ENDIF.
      ENDIF.
    ENDIF.

    IF rv_token IS INITIAL.
      MESSAGE '获取 Access Token 失败' TYPE 'E'.
    ENDIF.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Static Public Method ZCL_WECHAT_OFFICIAL_API=>SEND_NEWS_MESSAGE
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_ACCESS_TOKEN                TYPE        STRING
* | [--->] IV_OPENID                      TYPE        STRING
* | [--->] IT_ARTICLES                    TYPE        TY_ARTICLE_TAB
* | [<-()] RV_SUCCESS                     TYPE        ABAP_BOOL
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD send_news_message.
    DATA: lv_url    TYPE string,
          lv_json   TYPE string,
          lv_body   TYPE string,
          lv_articles TYPE string VALUE '',
          lv_first   TYPE abap_bool VALUE abap_true.

    "构建文章列表 JSON
    LOOP AT it_articles INTO DATA(ls_article).
      IF lv_first = abap_false.
        lv_articles = lv_articles && ','.
      ENDIF.
      lv_articles = lv_articles && |\{| &&
                   |"title":"{ escape( val = ls_article-title format = cl_abap_format=>e_json_string ) }",| &&
                   |"description":"{ escape( val = ls_article-description format = cl_abap_format=>e_json_string ) }"|.
      IF ls_article-url IS NOT INITIAL.
        lv_articles = lv_articles && |,"url":"{ escape( val = ls_article-url format = cl_abap_format=>e_json_string ) }"|.
      ENDIF.
      IF ls_article-picurl IS NOT INITIAL.
        lv_articles = lv_articles && |,"picurl":"{ escape( val = ls_article-picurl format = cl_abap_format=>e_json_string ) }"|.
      ENDIF.
      lv_articles = lv_articles && |\}|.
      lv_first = abap_false.
    ENDLOOP.

    "构建请求 URL
    lv_url = |https://api.weixin.qq.com/cgi-bin/message/custom/send?access_token={ iv_access_token }|.

    "构建请求体
    lv_body = |\{| &&
              |"touser":"{ iv_openid }",| &&
              |"msgtype":"news",| &&
              |"news":\{| &&
              |  "articles":[{ lv_articles }]| &&
              |\}| &&
              |\}|.

    "调用 API（使用 TRY-CATCH 处理异常）
    TRY.
        lv_json = call_api( iv_url = lv_url iv_method = 'POST' iv_body = lv_body ).
      CATCH cx_root INTO DATA(lo_error).
        "发生异常时记录错误并返回失败
        MESSAGE |发送图文消息失败: { lo_error->get_text( ) }| TYPE 'W'.
        rv_success = abap_false.
        RETURN.
    ENDTRY.

    "检查结果
    IF lv_json CS '"errcode":0'.
      rv_success = abap_true.
    ELSE.
      rv_success = abap_false.
      MESSAGE |发送图文消息失败: { lv_json }| TYPE 'W'.
    ENDIF.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Static Public Method ZCL_WECHAT_OFFICIAL_API=>SEND_TEXT_MESSAGE
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_ACCESS_TOKEN                TYPE        STRING
* | [--->] IV_OPENID                      TYPE        STRING
* | [--->] IV_CONTENT                     TYPE        STRING
* | [<-()] RV_SUCCESS                     TYPE        ABAP_BOOL
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD send_text_message.
    DATA: lv_url    TYPE string,
          lv_json   TYPE string,
          lv_body   TYPE string.

    "构建请求 URL
    lv_url = |https://api.weixin.qq.com/cgi-bin/message/custom/send?access_token={ iv_access_token }|.

    "构建请求体
    lv_body = |\{| &&
              |"touser":"{ iv_openid }",| &&
              |"msgtype":"text",| &&
              |"text":\{| &&
              |  "content":"{ escape( val = iv_content format = cl_abap_format=>e_json_string ) }"| &&
              |\}| &&
              |\}|.

    "调用 API（使用 TRY-CATCH 处理异常）
    TRY.
        lv_json = call_api( iv_url = lv_url iv_method = 'POST' iv_body = lv_body ).
      CATCH cx_root INTO DATA(lo_error).
        "发生异常时记录错误并返回失败
        MESSAGE |发送消息失败: { lo_error->get_text( ) }| TYPE 'W'.
        rv_success = abap_false.
        RETURN.
    ENDTRY.

    "检查结果
    IF lv_json CS '"errcode":0'.
      rv_success = abap_true.
    ELSE.
      rv_success = abap_false.
      MESSAGE |发送消息失败: { lv_json }| TYPE 'W'.
    ENDIF.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Static Public Method ZCL_WECHAT_OFFICIAL_API=>GET_OAUTH_URL
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_APPID                        TYPE        STRING
* | [--->] IV_REDIRECT_URI                 TYPE        STRING
* | [--->] IV_STATE                        TYPE        STRING (optional)
* | [--->] IV_SCOPE                        TYPE        STRING (default ='snsapi_base')
* | [<-()] RV_URL                          TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD get_oauth_url.
    DATA: lv_state TYPE string,
          lv_encoded_redirect TYPE string.

    "如果没有提供 state，生成一个随机值（用于防止 CSRF 攻击）
    IF iv_state IS INITIAL.
      "可以生成一个 GUID 或其他唯一标识
      TRY.
          lv_state = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
        CATCH cx_root.
          "如果生成失败，使用时间戳
          GET TIME STAMP FIELD DATA(lv_timestamp).
          lv_state = |{ lv_timestamp }|.
      ENDTRY.
    ELSE.
      lv_state = iv_state.
    ENDIF.

    "URL 编码回调地址（微信要求必须是 URL 编码）
    "注意：实际使用时，redirect_uri 应该是在微信公众平台配置的授权回调域名下的页面
    lv_encoded_redirect = escape( val = iv_redirect_uri format = cl_abap_format=>e_url ).

    "构建 OAuth 授权 URL
    "参考：https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/Wechat_webpage_authorization.html
    rv_url = |https://open.weixin.qq.com/connect/oauth2/authorize?| &&
             |appid={ iv_appid }| &&
             |&redirect_uri={ lv_encoded_redirect }| &&
             |&response_type=code| &&
             |&scope={ iv_scope }| &&
             |&state={ lv_state }| &&
             |#wechat_redirect|.

    "注意：scope 参数说明：
    " - snsapi_base: 静默授权，只能获取 openid（推荐，无需用户确认）
    " - snsapi_userinfo: 需要用户确认，可以获取用户基本信息
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Static Public Method ZCL_WECHAT_OFFICIAL_API=>GET_OPENID_BY_CODE
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_APPID                        TYPE        STRING
* | [--->] IV_APPSECRET                    TYPE        STRING
* | [--->] IV_CODE                         TYPE        STRING
* | [<-()] RV_OPENID                       TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD get_openid_by_code.
    DATA: lv_url    TYPE string,
          lv_json   TYPE string,
          lv_openid TYPE string,
          lv_access_token TYPE string.

    "构建获取 access_token 的 URL（通过 code）
    "参考：https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/Wechat_webpage_authorization.html
    lv_url = |https://api.weixin.qq.com/sns/oauth2/access_token?| &&
             |appid={ iv_appid }| &&
             |&secret={ iv_appsecret }| &&
             |&code={ iv_code }| &&
             |&grant_type=authorization_code|.

    "调用 API 获取 access_token 和 openid
    TRY.
        lv_json = call_api( iv_url = lv_url ).
      CATCH cx_root INTO DATA(lo_error).
        "发生异常时记录错误并返回空
        MESSAGE |获取 OpenID 失败: { lo_error->get_text( ) }| TYPE 'W'.
        rv_openid = ''.
        RETURN.
    ENDTRY.

    "检查响应是否为空
    IF lv_json IS INITIAL.
      MESSAGE '获取 OpenID 失败：响应为空' TYPE 'W'.
      rv_openid = ''.
      RETURN.
    ENDIF.

    "解析 JSON 响应
    "格式：{"access_token":"ACCESS_TOKEN","expires_in":7200,"refresh_token":"REFRESH_TOKEN","openid":"OPENID","scope":"SCOPE"}
    "或者错误：{"errcode":40029,"errmsg":"invalid code"}
    
    "检查是否包含错误码
    IF lv_json CS '"errcode"'.
      "提取错误信息
      FIND FIRST OCCURRENCE OF '"errmsg":"' IN lv_json
        MATCH OFFSET DATA(lv_offset).
      IF sy-subrc = 0.
        DATA(lv_start) = lv_offset + 10.
        FIND FIRST OCCURRENCE OF '"' IN SECTION OFFSET lv_start OF lv_json
          MATCH OFFSET DATA(lv_end).
        IF sy-subrc = 0.
          DATA(lv_len) = lv_end - lv_start.
          DATA(lv_error_msg) = lv_json+lv_start(lv_len).
          MESSAGE |获取 OpenID 失败: { lv_error_msg }| TYPE 'W'.
        ENDIF.
      ENDIF.
      rv_openid = ''.
      RETURN.
    ENDIF.

    "提取 openid
    FIND FIRST OCCURRENCE OF '"openid":"' IN lv_json
      MATCH OFFSET lv_offset.
    IF sy-subrc = 0.
      lv_start = lv_offset + 9.
      FIND FIRST OCCURRENCE OF '"' IN SECTION OFFSET lv_start OF lv_json
        MATCH OFFSET lv_end.
      IF sy-subrc = 0.
        lv_len = lv_end - lv_start.
        rv_openid = lv_json+lv_start(lv_len).
      ENDIF.
    ENDIF.

    IF rv_openid IS INITIAL.
      MESSAGE '获取 OpenID 失败：未找到 openid 字段' TYPE 'W'.
    ENDIF.

    "注意：返回的 access_token 和 refresh_token 可以保存用于后续调用用户信息接口
    "但通常只需要保存 openid 用于发送消息
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Protected Method ZCL_WECHAT_OFFICIAL_API=>CALL_API_INSTANCE
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_URL                           TYPE        STRING
* | [--->] IV_METHOD                        TYPE        STRING (default ='GET')
* | [--->] IV_BODY                          TYPE        STRING(optional)
* | [<-()] RV_JSON                          TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD call_api_instance.
    "实例方法版本的 call_api，使用实例变量 mv_destination
    "参考 ZCL_CORPWEIXIN_APPROVAL_API 中 mv_destination 的使用方式
    
    "调用静态方法，但使用实例的 destination
    rv_json = call_api(
      iv_url         = iv_url
      iv_method      = iv_method
      iv_body        = iv_body
      iv_destination = mv_destination
    ).
  ENDMETHOD.
ENDCLASS.