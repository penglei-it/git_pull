class ZCL_CORPWEIXIN_APPROVAL_API definition
  public
  final
  create public .

public section.

  types:
    BEGIN OF ts_applyer,
        userid  TYPE string,
        partyid TYPE string,
      END OF ts_applyer .
  types:
    BEGIN OF ts_notifyer,
        userid TYPE string,
      END OF ts_notifyer .
  types:
    BEGIN OF ts_sprecorddetail,
        approver  TYPE ts_notifyer,
        speech    TYPE string,
        sp_status TYPE string,
        sptime    TYPE string,
        media_id  TYPE string,
      END OF ts_sprecorddetail .
  types:
    BEGIN OF ts_sprecord,
        sp_status    TYPE string,
        approverattr TYPE string,
        details      TYPE STANDARD TABLE OF ts_sprecorddetail WITH DEFAULT KEY,
      END OF ts_sprecord .
  types:
    BEGIN OF ts_element,
        text TYPE string,
        lang TYPE string,
      END OF ts_element .
  types:
    BEGIN OF ts_member,
        userid TYPE string,
        name   TYPE string,
      END OF ts_member .
  types:
    BEGIN OF ts_appl_date,
        type        TYPE string,
        s_timestamp TYPE string,
      END OF ts_appl_date .
  types:
    BEGIN OF ts_appl_selopt,
        key   TYPE string,
        value TYPE STANDARD TABLE OF ts_element WITH DEFAULT KEY,
      END OF ts_appl_selopt .
  types:
    BEGIN OF ts_appl_selector,
        type    TYPE string,
        options TYPE STANDARD TABLE OF ts_appl_selopt WITH DEFAULT KEY,
      END OF ts_appl_selector .
  types:
    BEGIN OF ts_depart,
        openapi_id TYPE string,
        name       TYPE string,
      END OF ts_depart .
  types:
    BEGIN OF ts_file,
        file_id TYPE string,
      END OF ts_file .
  types:
    BEGIN OF ts_daterange,
        type         TYPE string,
        new_begin    TYPE string,
        new_end      TYPE string,
        new_duration TYPE string,
      END OF ts_daterange .
  types:
    BEGIN OF ts_location,
        latitude  TYPE string,
        longitude TYPE string,
        title     TYPE string,
        address   TYPE string,
        time      TYPE string,
      END OF ts_location .
  types:
    BEGIN OF ts_applydatcont_value,
        text        TYPE string,
        new_number  TYPE string,
        new_money   TYPE string,
        date        TYPE ts_appl_date,
        selector    TYPE ts_appl_selector,
        members     TYPE STANDARD TABLE OF ts_member WITH DEFAULT KEY,
        departments TYPE STANDARD TABLE OF ts_depart WITH DEFAULT KEY,
        files       TYPE STANDARD TABLE OF ts_file WITH DEFAULT KEY,
        date_range  TYPE STANDARD TABLE OF ts_daterange WITH DEFAULT KEY,
        location    TYPE STANDARD TABLE OF ts_location WITH DEFAULT KEY,
      END OF ts_applydatcont_value .
  types:
    BEGIN OF ts_applydatacontent,
        control TYPE string,
        id      TYPE string,
        title   TYPE STANDARD TABLE OF ts_element WITH DEFAULT KEY,
        value   TYPE ts_applydatcont_value,
      END OF ts_applydatacontent .
  types:
    BEGIN OF ts_applydata,
        contents TYPE STANDARD TABLE OF ts_applydatacontent WITH DEFAULT KEY,
      END OF ts_applydata .
  types:
    BEGIN OF ts_comment,
        commentuserinfo TYPE ts_notifyer,
        commenttime     TYPE string,
        commentcontent  TYPE string,
        commentid       TYPE string,
        media_id        TYPE STANDARD TABLE OF string WITH DEFAULT KEY,
      END OF ts_comment .
  types:
    BEGIN OF ts_info,
        sp_no       TYPE string,
        sp_name     TYPE string,
        sp_status   TYPE string,
        template_id TYPE string,
        apply_time  TYPE string,
        applyer     TYPE  ts_applyer,
        sp_record   TYPE STANDARD TABLE OF ts_sprecord WITH DEFAULT KEY,
        notifyer    TYPE STANDARD TABLE OF ts_notifyer WITH DEFAULT KEY,
        apply_data  TYPE ts_applydata,
        comments    TYPE STANDARD TABLE OF ts_comment WITH DEFAULT KEY,
      END OF ts_info .
  types:
    BEGIN OF ts_approvaldetail,
        errcode TYPE string,
        errmsg  TYPE string,
        info    TYPE ts_info,
      END OF ts_approvaldetail .
  types:
    tt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i WITH DEFAULT KEY .

  data MV_AGENTTOKEN type ZTICERP_DING_TK-ACCESS_TOKEN read-only .
  data MV_DESTINATION type RFCDES-RFCDEST read-only .

  methods PROCESS_APPROVAL
    importing
      value(IV_SPNO) type STRING
      value(IV_TEMPLATEID) type STRING
      value(IV_TYPE) type STRING .
  methods CONSTRUCTOR
    importing
      value(IV_TOKEN) type ZTICERP_DING_TK-ACCESS_TOKEN optional
      value(IV_DESTINATION) type RFCDES-RFCDEST .
  methods GET_APPROVAL_DETAIL_BY_SPNO
    importing
      value(SPNO) type STRING
    returning
      value(APPROVAL_DETAIL) type TS_APPROVALDETAIL .
  methods APPLY_ICEC_CUSTOMER_BLUELIST
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods UPDATE_REPAIRCLAIM_DATA
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods APPLY_YUN_FREEPOSTAGECOUPON
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods APPLY_AFTERSALE_REFUSE
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods UPDATE_AFCLAIM_DATA
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods TYPECHANGE_ICEC_CLOSE
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods SPECHECKOUT_ICEC_CLOSE
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods SEND_ICEC_CUSTOMER_COIN_V2_NEW
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING
      value(IV_CODE) type STRING optional
      value(IV_NUMBER) type INT4 optional
      value(IV_NUMWITHDECIMAL) type DMBTR optional
      value(IV_USERLOGINID) type STRING optional
      value(IS_DETAIL) type TS_APPROVALDETAIL optional
    exporting
      value(IS_DP_PI_H) type ZTINT_DP_PI_H
      value(IV_SUCCESSNUM) type INT4 .
  methods SEND_ICEC_CUSTOMER_COIN_V2
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING
      value(IV_CODE) type STRING optional
      value(IV_NUMBER) type INT4 optional
      value(IV_NUMWITHDECIMAL) type DMBTR optional
      value(IS_DETAIL) type TS_APPROVALDETAIL optional
    exporting
      value(IS_DP_PI_H) type ZTINT_DP_PI_H
      value(IV_SUCCESSNUM) type INT4 .
  methods SEND_ICEC_CUSTOMER_COIN_HQ
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING
      value(IV_CODE) type STRING optional
      value(IV_NUMBER) type INT4 optional
      value(IV_NUMWITHDECIMAL) type DMBTR optional
      value(IS_DETAIL) type TS_APPROVALDETAIL optional
    exporting
      value(IS_DP_PI_H) type ZTINT_DP_PI_H
      value(IV_SUCCESSNUM) type INT4 .
  methods APPROVER_POINTS_DISTRIBUTE_V2
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING
      value(IV_CODE) type STRING optional
      value(IV_POINTS) type INT4 optional
      value(IV_USERLOGINID) type STRING optional
      value(IS_DETAIL) type TS_APPROVALDETAIL optional
    exporting
      value(IS_DP_PI_H) type ZTINT_DP_PI_H
      value(IV_SUCCESSNUM) type INT4 .
  methods APPROVER_POINTS_DISTRIBUTE_NEW
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING
      value(IV_CODE) type STRING optional
      value(IV_NUMBER) type INT4 optional
      value(IV_USERLOGINID) type STRING optional
      value(IS_DETAIL) type TS_APPROVALDETAIL optional
    exporting
      value(IS_DP_PI_H) type ZTINT_DP_PI_H
      value(IV_SUCCESSNUM) type INT4 .
  methods APPROVER_MARKET_MANAGE_PRO_V2
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods APPROVER_MARKET_DISTRIBUTE_V2
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods APPROVER_FILE_TO_OBS
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING
    exporting
      value(EV_FILE) type XSTRING .
  methods APPROVER_ONLY_NOT_SEND
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods APPROVER_MARKET_MANAGE_PROCESS
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods SEND_ICEC_CUSTOMER_COIN
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods DELAY_ICEC_CREDIT_CLOSE
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods CLOSECREDIT_ICEC_CLOSE
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods APPLY_VENDOR_SPECIALPOLICY
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods APPLY_VENDOR_ONLINE
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods APPLY_VENDOR_OFFLINE
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods APPLY_VENDOR_QUALIFY
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods APPLY_VENDOR_ACCESS
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods APPLY_TEMP_CLASS
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods APPLY_STORE_OPEN
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods APPLY_PURCHASEEXC_COMPENSATE
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods APPLY_ICEC_SPECIALCOUPON
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods APPLY_ICEC_COUPON_ISSUE_HQ
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING
      value(IV_CODE) type STRING optional
      value(IV_NUMBER) type INT4 optional
      value(IS_DETAIL) type TS_APPROVALDETAIL optional
    exporting
      value(IS_DP_PI_H) type ZTINT_DP_PI_H
      value(IV_SUCCESSNUM) type INT4 .
  methods APPLY_ICEC_SPECIALCOUPON_NEW
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING
      value(IV_CODE) type STRING optional
      value(IV_NUMBER) type INT4 optional
      value(IS_DETAIL) type TS_APPROVALDETAIL optional
      value(IV_USERLOGINID) type STRING optional
    exporting
      value(IS_DP_PI_H) type ZTINT_DP_PI_H
      value(IV_SUCCESSNUM) type INT4 .
  methods APPLY_ICEC_SPECIALCOUPON_V2
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING
      value(IV_CODE) type STRING optional
      value(IV_NUMBER) type INT4 optional
      value(IS_DETAIL) type TS_APPROVALDETAIL optional
      value(IV_USERLOGINID) type STRING optional
    exporting
      value(IS_DP_PI_H) type ZTINT_DP_PI_H
      value(IV_SUCCESSNUM) type INT4 .
  methods APPLY_ICEC_REGISTERCOUPON
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods APPLY_COUPON_ADDAMOUNT
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods APPLY_COUPON
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods ADD_ICEC_CUSTOMER_TEMPCREDIT
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods UPDATE_CUSTOMER_VOLUMECLASS
    importing
      value(IV_SPNO) type STRING
      value(IV_TYPE) type STRING .
  methods SEND_DPINFO_TO_ICEC
    importing
      value(IV_SPNO) type STRING .
  methods SAVE_BASIC_INFO
    importing
      value(IS_DETAIL) type TS_APPROVALDETAIL optional
    exporting
      value(ES_HWOBS) type ZTFI_HWOBS_LOG .
protected section.
private section.

  methods PREPARE_ZTINT_DP_COIN_L
    importing
      value(EVENTID) type STRING
      value(EVENTDESC) type STRING
      value(STATUS) type STRING
      value(MESSAGE) type STRING
      value(KEY1) type STRING optional
      value(KEY2) type STRING optional
      value(KEY3) type STRING optional
      value(KEY4) type STRING optional
      value(KEY5) type STRING optional
    returning
      value(DP_PI_L) type ZTINT_DP_PI_L .
  methods PREPARE_ZTINT_DP_PI_L
    importing
      value(EVENTID) type STRING
      value(EVENTDESC) type STRING
      value(STATUS) type STRING
      value(MESSAGE) type STRING
      value(KEY1) type STRING optional
      value(KEY2) type STRING optional
      value(KEY3) type STRING optional
      value(KEY4) type STRING optional
      value(KEY5) type STRING optional
      value(CONTENT) type STRING optional
    returning
      value(DP_PI_L) type ZTINT_DP_PI_L .
  methods LOG
    importing
      !IS_LOG type ZTICCRM_LOG
      !IV_COMMIT type CHAR1 default '' .
  methods SAVE_API_LOG
    importing
      value(EV_MSG) type BAPIRET2 optional
      value(IV_COMMIT) type CHAR01 optional
      value(IV_KEYVALUE) type STRING optional
      value(IV_REQUESTBODY) type STRING optional
      value(IV_RESPONSEBODY) type STRING optional .
  methods LOG_ZTINT_DP_PI_LOG
    importing
      value(ZEVENTID) type STRING optional
      value(ZEVENT) type STRING optional
      value(ZMSG) type STRING optional
      value(ZVALUE1) type STRING optional
      value(ZVALUE2) type STRING optional
      value(SENDMSG) type STRING optional
      value(FNAME) type STRING optional .
  methods PREPARE_ZTINT_DP_PI_H
    importing
      value(IS_DETAIL) type TS_APPROVALDETAIL
      value(IV_TYPE) type STRING
    returning
      value(DP_PI_H) type ZTINT_DP_PI_H .
  methods PREPARE_ZTINT_DP_PI_I
    importing
      value(IS_CONTENT) type TS_APPLYDATACONTENT
      value(IV_SPNO) type STRING
    returning
      value(DP_PI_I) type ZTINT_DP_PI_I .
ENDCLASS.



CLASS ZCL_CORPWEIXIN_APPROVAL_API IMPLEMENTATION.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->ADD_ICEC_CUSTOMER_TEMPCREDIT
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD add_icec_customer_tempcredit.
    DATA(lo_formatter) = NEW zcl_cassint_formatter( ).
    DATA:ls_ztint_dp_tmct_l TYPE ztint_dp_tmct_l.
    DATA:lt_ztint_dp_tmct_h TYPE STANDARD TABLE OF ztint_dp_tmct_h,
         ls_ztint_dp_tmct_h LIKE LINE OF lt_ztint_dp_tmct_h,
         lt_ztint_dp_tmct_i TYPE STANDARD TABLE OF ztint_dp_tmct_i,
         ls_ztint_dp_tmct_i LIKE LINE OF lt_ztint_dp_tmct_i.
    DATA:lt_user_content TYPE  zsint_push_userlist_tab,
         ls_user_content TYPE  zsint_push_userlist.
    DATA:lv_creditapply   TYPE zde_intamount,
         lv_creditapply_s TYPE string.
    DATA:lv_body          TYPE string,
         lv_instance      TYPE zde_intprocessinstanceid,
         lv_alert_content TYPE string,
         lv_fname         TYPE string,
         lv_url           TYPE string,
         lv_title         TYPE string,
         lv_msg_type      TYPE string,
         lv_content       TYPE string,
         lv_date_s        TYPE string.

    DATA:
      lv_type TYPE bapi_mtype,
      lv_msg  TYPE bapi_msg.

    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
    DATA(lv_code) = ls_detail-info-template_id.

    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
                           sendmsg = 'X' fname = 'TEMPCREDIT' ).
      RETURN.
    ENDIF.

    IF iv_spno IS NOT INITIAL.
      SELECT SINGLE * INTO @DATA(ls_tempcredit_data) FROM ztint_tempcredit WHERE instaceid EQ @iv_spno.
      IF sy-subrc EQ 0 AND ls_tempcredit_data-synstatus NE 'Y'.
        SELECT * INTO TABLE @DATA(lt_file) FROM ztint_dp_file WHERE guid EQ @ls_tempcredit_data-guid.
        CALL FUNCTION 'Z_FMINT_TEMPCREDIT_DATA_SYN'
          EXPORTING
            is_ztint_tempcredit = ls_tempcredit_data
          IMPORTING
            ev_type             = lv_type
            ev_msg              = lv_msg
          TABLES
            it_file             = lt_file.
        IF lv_type EQ 'S'.
          UPDATE ztint_tempcredit SET synstatus = 'Y'
                                      zupd_uname = sy-uname
                                      zupd_bdate = sy-datum
                                      zupd_btime = sy-uzeit
                                WHERE instaceid = iv_spno.

        ELSE.
          "日志
          TRY .
              ls_ztint_dp_tmct_l-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
            CATCH cx_uuid_error.
          ENDTRY.
          ls_ztint_dp_tmct_l-event_desc = '临时额度申请同步金融'.
          ls_ztint_dp_tmct_l-event_id = 'tempcredit_apply_syn'.
          ls_ztint_dp_tmct_l-status = 'E'.
          ls_ztint_dp_tmct_l-message = lv_msg.
          ls_ztint_dp_tmct_l-key_value1 = iv_spno.
          ls_ztint_dp_tmct_l-key_value2 = lv_code.
          ls_ztint_dp_tmct_l-zcrt_bdate = sy-datum.
          ls_ztint_dp_tmct_l-zcrt_btime = sy-uzeit.
          MODIFY ztint_dp_tmct_l FROM ls_ztint_dp_tmct_l.

          CLEAR lv_date_s.
          CLEAR lv_content.
          lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
          CLEAR lv_creditapply.
          CLEAR lv_creditapply_s.
          lv_creditapply = ls_tempcredit_data-creditapply / 10000.
          lv_creditapply_s = lv_creditapply.
          CONDENSE lv_creditapply_s NO-GAPS .
          lv_content =   '开思助手 \n '   && |您申请的【临时额度】同步金融平台失败：{ lv_msg }，请知悉！ \n|
                        && '客户名称:' && ls_tempcredit_data-companyname && ' \n '
                        && '客户代码:' && ls_tempcredit_data-companycode && ' \n '
                        && '临时额度:' && lv_creditapply_s && '万' &&  ' \n '
                        && lv_date_s.
          CLEAR ls_user_content.
          ls_user_content-content = lv_content.
          ls_user_content-userid = ls_tempcredit_data-originatoruserid.
          ls_user_content-orderno = |{ ls_tempcredit_data-companycode }|.
          ls_user_content-orderdesc = |{ ls_tempcredit_data-companyname }|.
          ls_user_content-firstcatgory = '客户管理'.
          ls_user_content-secondcatgory = '临时额度申请审批'.
          APPEND ls_user_content TO lt_user_content.
*-
          DATA(lv_status) = SWITCH char10( iv_type
                                            WHEN 'agree' THEN 'APPROVE'
                                            WHEN 'refuse' THEN 'UNAPPROVE'
                                            WHEN 'terminate' THEN 'CANCELED'
                                         ).
          UPDATE ztint_tempcredit SET status = lv_status
                                      msg = lv_msg
                                      zupd_bdate = sy-datum
                                      zupd_btime = sy-uzeit
                                      zupd_uname = '企业微信'
                                WHERE instaceid = iv_spno.
*   推送通知
          IF lt_user_content IS NOT INITIAL.
            lv_title =  '开思助手'.
            lv_msg_type = 'text'.
            CONDENSE lv_url NO-GAPS .

            CALL FUNCTION 'Z_FMQYWX_CASSINT_PUSH_MESSAGE'
              IN UPDATE TASK
              EXPORTING
                iv_title    = lv_title
                iv_msgtype  = lv_msg_type
                iv_url      = lv_url
              TABLES
                it_userlist = lt_user_content.
          ENDIF.
          COMMIT WORK AND WAIT.
          RETURN.
        ENDIF.
      ENDIF.
    ENDIF.

    "审批结果
    CASE iv_type.
      WHEN 'agree'. "审批通过
        "查找申请记录
        SELECT SINGLE * FROM ztint_tempcredit INTO @DATA(ls_ztint_tempcredit) WHERE instaceid = @iv_spno.
        IF sy-subrc NE 0.
          TRY .
              ls_ztint_dp_tmct_l-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
            CATCH cx_uuid_error.
          ENDTRY.

          ls_ztint_dp_tmct_l-event_desc = '临时额度流程ID'.
          ls_ztint_dp_tmct_l-event_id = 'instanceid_not_found'.
          ls_ztint_dp_tmct_l-status = 'E'.
          ls_ztint_dp_tmct_l-message = '未找到对应的申请记录'.
          ls_ztint_dp_tmct_l-key_value2 = lv_code.
          ls_ztint_dp_tmct_l-zcrt_bdate = sy-datum.
          ls_ztint_dp_tmct_l-zcrt_btime = sy-uzeit.
          MODIFY ztint_dp_tmct_l FROM ls_ztint_dp_tmct_l.
          RETURN.
        ENDIF.
        "判断这个实例是否已经加过

        SELECT SINGLE * FROM ztint_dp_tmct_h INTO CORRESPONDING FIELDS OF @ls_ztint_dp_tmct_h
          WHERE processinstanceid =  @iv_spno.
        IF ls_ztint_dp_tmct_h-sendstatus = 'Y'. "已发放
          "已发放记录日志
          TRY .
              ls_ztint_dp_tmct_l-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
            CATCH cx_uuid_error.
          ENDTRY.

          ls_ztint_dp_tmct_l-event_desc = '实例临时额度'.
          ls_ztint_dp_tmct_l-event_id = 'send_tmct_already'.
          ls_ztint_dp_tmct_l-status = 'E'.
          ls_ztint_dp_tmct_l-message = '实例已发放过临时额度'.
          ls_ztint_dp_tmct_l-key_value1 = iv_spno.
          ls_ztint_dp_tmct_l-key_value2 = lv_code.
          ls_ztint_dp_tmct_l-zcrt_bdate = sy-datum.
          ls_ztint_dp_tmct_l-zcrt_btime = sy-uzeit.
          MODIFY ztint_dp_tmct_l FROM ls_ztint_dp_tmct_l.
          RETURN.
        ENDIF.

        lv_instance = iv_spno.

        "锁表
        CALL FUNCTION 'ENQUEUE_EZ_INT_DP_TMCT_H'
          EXPORTING
            mode_ztint_dp_tmct_h = 'E'
            mandt                = sy-mandt
            instanceid           = lv_instance.
        IF sy-subrc <> 0.
          "锁表失败 记录日志
          TRY .
              ls_ztint_dp_tmct_l-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
            CATCH cx_uuid_error.
          ENDTRY.

          ls_ztint_dp_tmct_l-event_desc = '临时额度锁表失败'.
          ls_ztint_dp_tmct_l-event_id = 'lock_ztint_tmct_h_fail'.
          ls_ztint_dp_tmct_l-status = 'E'.
          ls_ztint_dp_tmct_l-message = '实例临时额度锁表失败'.
          ls_ztint_dp_tmct_l-key_value1 = iv_spno.
          ls_ztint_dp_tmct_l-key_value2 = lv_code.
          ls_ztint_dp_tmct_l-zcrt_bdate = sy-datum.
          ls_ztint_dp_tmct_l-zcrt_btime = sy-uzeit.
          MODIFY ztint_dp_tmct_l FROM ls_ztint_dp_tmct_l.
          RETURN.
        ENDIF.

        "抬头信息
        DATA: lv_timestamp TYPE p.
        "没增加过
        "抬头信息赋值
        ls_ztint_dp_tmct_h-processinstanceid = ls_detail-info-sp_no.
        ls_ztint_dp_tmct_h-processcode = ls_detail-info-template_id.
        ls_ztint_dp_tmct_h-title = ls_detail-info-sp_name.
*        dp_pi_h-businessid = is_detail-business_id.
        ls_ztint_dp_tmct_h-zresult = iv_type.
        lv_timestamp  = ls_detail-info-apply_time."创建时间
        IF lv_timestamp > 0.
          zcl_cassint_formatter=>convert_unix_timestamp_to_abap(
            EXPORTING unixsecs = lv_timestamp
            IMPORTING ev_date = ls_ztint_dp_tmct_h-createdate ev_time = ls_ztint_dp_tmct_h-createtime ).
          ls_ztint_dp_tmct_h-createstamp = lv_timestamp.
          CONDENSE ls_ztint_dp_tmct_h-createstamp.
        ENDIF.
        CLEAR lv_timestamp.

        DATA(lv_notes) = lines( ls_detail-info-sp_record )."多少审批节点
*        DATA(lv_time) = lines( ls_detail-info-sp_record[ lv_notes ]-details )."每个审批节点几个人依次审批
*        lv_timestamp = VALUE #( ls_detail-info-sp_record[ lv_notes ]-details[ lv_time ]-sptime OPTIONAL ).
        DATA(lt_time) = ls_detail-info-sp_record[ lv_notes ]-details."每个审批节点几个人依次审批
        SORT lt_time BY sptime DESCENDING.
        READ TABLE lt_time INTO DATA(ls_time) INDEX 1.
        lv_timestamp = ls_time-sptime.
        IF lv_timestamp > 0.
          zcl_cassint_formatter=>convert_unix_timestamp_to_abap(
            EXPORTING unixsecs = lv_timestamp
            IMPORTING ev_date = ls_ztint_dp_tmct_h-finishdate ev_time = ls_ztint_dp_tmct_h-finishtime ).
          ls_ztint_dp_tmct_h-finishstamp = lv_timestamp.
          CONDENSE ls_ztint_dp_tmct_h-finishstamp.
        ENDIF.
        CLEAR lv_timestamp.
        ls_ztint_dp_tmct_h-status = 'finish'.
        ls_ztint_dp_tmct_h-originatordeptid = ls_detail-info-applyer-partyid.
        ls_ztint_dp_tmct_h-originatoruserid = ls_detail-info-applyer-userid.
        ls_ztint_dp_tmct_h-senddate = sy-datum.
        ls_ztint_dp_tmct_h-sendtime = sy-uzeit.

        "审批详情
        LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
          CLEAR ls_ztint_dp_tmct_i.
          ls_ztint_dp_tmct_i-processinstanceid = iv_spno.
          ls_ztint_dp_tmct_i-posnr = sy-tabix.
          ls_ztint_dp_tmct_i-name = VALUE #( ls_content-title[ 1 ]-text OPTIONAL ).
          CASE ls_content-control.
            WHEN 'Text' OR 'Textarea'.
              ls_ztint_dp_tmct_i-value = ls_content-value-text.
            WHEN 'Number'.
              ls_ztint_dp_tmct_i-value = ls_content-value-new_number.
            WHEN 'Money'.
              ls_ztint_dp_tmct_i-value = ls_content-value-new_money.
            WHEN 'Selector'.
              ls_ztint_dp_tmct_i-value = VALUE #( ls_content-value-selector-options[ 1 ]-value[ 1 ]-text OPTIONAL ).
            WHEN  OTHERS.
          ENDCASE.
          APPEND ls_ztint_dp_tmct_i TO lt_ztint_dp_tmct_i.
        ENDLOOP.

        NEW zcl_icec_finance_api( )->tempcredit_approval_syn(
            EXPORTING is_ztint_tempcredit = ls_ztint_tempcredit
                      is_detail_qywx = ls_detail
                      iv_qywx = 'X'
                      iv_type = iv_type
            IMPORTING es_res = DATA(ls_respon) ).
        IF ls_respon-code EQ '200'."同步成功
          TRY .
              ls_ztint_dp_tmct_l-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
            CATCH cx_uuid_error.
          ENDTRY.

          ls_ztint_dp_tmct_l-event_desc = '调用接口增加临时额度成功'.
          ls_ztint_dp_tmct_l-event_id = 'add_tempcredit_success'.
          ls_ztint_dp_tmct_l-status = 'S'.
          ls_ztint_dp_tmct_l-message = '调用接口增加临时额度成功'.
          ls_ztint_dp_tmct_l-key_value1 = iv_spno.
          ls_ztint_dp_tmct_l-key_value2 = lv_code.
          ls_ztint_dp_tmct_l-zcrt_bdate = sy-datum.
          ls_ztint_dp_tmct_l-zcrt_btime = sy-uzeit.
          MODIFY ztint_dp_tmct_l FROM ls_ztint_dp_tmct_l.

          "临时额度审批抬头表
          ls_ztint_dp_tmct_h-sendstatus = 'Y'."发放成功
          ls_ztint_dp_tmct_h-senddate = sy-datum.
          ls_ztint_dp_tmct_h-sendtime = sy-uzeit.
          ls_ztint_dp_tmct_h-sendmsg = '调用接口增加临时额度成功'.

          "临时额度信息表
          ls_ztint_tempcredit-msg = '额度已生效'.
          ls_ztint_tempcredit-status = 'APPROVE'."已审批
          ls_ztint_tempcredit-zupd_bdate = sy-datum.
          ls_ztint_tempcredit-zupd_btime = sy-uzeit.
          ls_ztint_tempcredit-zupd_uname = '企业微信'.

          IF ls_ztint_tempcredit-originatoruserid IS NOT INITIAL.
            "通知申请人额度生效
            CLEAR lv_date_s.
            CLEAR lv_content.
            lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
            CLEAR lv_creditapply.
            CLEAR lv_creditapply_s.
            lv_creditapply = ls_ztint_tempcredit-creditapply / 10000.
            lv_creditapply_s = lv_creditapply.
            CONDENSE lv_creditapply_s NO-GAPS .

            lv_content =   '开思助手 \n '   && '您申请的【临时额度】已生效，请知悉！ \n '
             && '客户名称:' && ls_ztint_tempcredit-companyname && ' \n '
             && '客户代码:' && ls_ztint_tempcredit-companycode && ' \n '
             && '临时额度:' && lv_creditapply_s && '万' && ' \n '
             && lv_date_s.

            CLEAR ls_user_content.
            ls_user_content-content = lv_content.
            ls_user_content-userid = ls_ztint_tempcredit-originatoruserid.
            ls_user_content-orderno = |{ ls_ztint_tempcredit-companycode }|.
            ls_user_content-orderdesc = |{ ls_ztint_tempcredit-companyname }|.
            ls_user_content-firstcatgory = '客户管理'.
            ls_user_content-secondcatgory = '临时额度申请审批'.
            APPEND ls_user_content TO lt_user_content.
          ENDIF.
          IF ls_ztint_tempcredit-isspcreditapply EQ 'Y'.
            SELECT SINGLE * INTO @DATA(ls_spcredit) FROM ztint_spcredit WHERE yearmonth = @ls_ztint_tempcredit-zcrt_bdate+0(6)
                AND deptid = @ls_ztint_tempcredit-spcreditdeptid.
            IF sy-subrc EQ 0.
              ls_spcredit = VALUE #( BASE ls_spcredit freezecredit = ls_spcredit-freezecredit - lv_creditapply
                                     zupd_bdate = sy-datum zupd_btime = sy-uzeit zupd_uname = '钉钉' ).
              MODIFY ztint_spcredit FROM ls_spcredit.
            ENDIF.
          ENDIF.
        ELSE.
          TRY .
              ls_ztint_dp_tmct_l-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
            CATCH cx_uuid_error.
          ENDTRY.

          "调用接口失败
          ls_ztint_dp_tmct_l-event_desc = '调用接口增加临时额度失败'.
          ls_ztint_dp_tmct_l-event_id = 'add_icec_custmer_tempcredit'.
          ls_ztint_dp_tmct_l-status = 'E'.
          ls_ztint_dp_tmct_l-message = '调用接口增加临时额度失败'.
          ls_ztint_dp_tmct_l-key_value1 = iv_spno.
          ls_ztint_dp_tmct_l-key_value2 = lv_code.
          ls_ztint_dp_tmct_l-key_value3 = ls_respon-desc.
          ls_ztint_dp_tmct_l-zcrt_bdate = sy-datum.
          ls_ztint_dp_tmct_l-zcrt_btime = sy-uzeit.
          MODIFY ztint_dp_tmct_l FROM ls_ztint_dp_tmct_l.
          "临时额度审批抬头表

          ls_ztint_dp_tmct_h-sendstatus = 'N'."未成功
          ls_ztint_dp_tmct_h-senddate = sy-datum.
          ls_ztint_dp_tmct_h-sendtime = sy-uzeit.
          ls_ztint_dp_tmct_h-sendmsg = ls_respon-desc.

          "临时额度信息表
          ls_ztint_tempcredit-msg = ls_respon-desc.
          ls_ztint_tempcredit-status = 'APPROVE'."已审批
          ls_ztint_tempcredit-zupd_bdate = sy-datum.
          ls_ztint_tempcredit-zupd_btime = sy-uzeit.
          ls_ztint_tempcredit-zupd_uname = '企业微信'.
          lv_creditapply = ls_ztint_tempcredit-creditapply / 10000.
          IF ls_ztint_tempcredit-isspcreditapply EQ 'Y'.
            SELECT SINGLE * INTO @ls_spcredit FROM ztint_spcredit WHERE yearmonth = @ls_ztint_tempcredit-zcrt_bdate+0(6)
              AND deptid = @ls_ztint_tempcredit-spcreditdeptid.
            IF sy-subrc EQ 0.
              ls_spcredit = VALUE #( BASE ls_spcredit freezecredit = ls_spcredit-freezecredit - lv_creditapply
                                     availablecredit = ls_spcredit-availablecredit + lv_creditapply
                                     zupd_bdate = sy-datum zupd_btime = sy-uzeit zupd_uname = '企业微信' ).
              MODIFY ztint_spcredit FROM ls_spcredit.
            ENDIF.
          ENDIF.

          "推送内部人员处理
          CLEAR lv_date_s.
          CLEAR lv_content.
          lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

          lv_alert_content = '开思助手 \n '  && ls_ztint_dp_tmct_h-title  && '审批后调用电商接口失败,请关注! \n '
             && '实例号：' && ls_ztint_dp_tmct_h-processinstanceid && ' \n '
             && '流程code：' && ls_ztint_dp_tmct_h-processcode && ' \n '
             && lv_date_s.

          lv_fname = 'DPTEMPCREDIT'.
          lv_title =  '开思助手'.
          lv_msg_type = 'text'.

          CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
            EXPORTING
              iv_content = lv_alert_content
              iv_fname   = lv_fname.

*-LONGLF ADD IN 20210619 推送钉钉
          CLEAR lv_creditapply.
          CLEAR lv_creditapply_s.
          lv_creditapply = ls_ztint_tempcredit-creditapply / 10000.
          lv_creditapply_s = lv_creditapply.
          CONDENSE lv_creditapply_s NO-GAPS .
          lv_content =   '开思助手 \n '   && |您申请的【临时额度】申请失败：{ ls_respon-desc }，请知悉！ \n|
                        && '客户名称:' && ls_ztint_tempcredit-companyname && ' \n '
                        && '客户代码:' && ls_ztint_tempcredit-companycode && ' \n '
                        && '临时额度:' && lv_creditapply_s && '万' &&  ' \n '
                        && lv_date_s.
          CLEAR ls_user_content.
          ls_user_content-content = lv_content.
          ls_user_content-userid = ls_ztint_tempcredit-originatoruserid.
          ls_user_content-orderno = |{ ls_ztint_tempcredit-companycode }|.
          ls_user_content-orderdesc = |{ ls_ztint_tempcredit-companyname }|.
          ls_user_content-firstcatgory = '客户管理'.
          ls_user_content-secondcatgory = '临时额度申请审批'.
          APPEND ls_user_content TO lt_user_content.
*-END OF ADD
        ENDIF.
        "更新表
        MODIFY ztint_tempcredit FROM ls_ztint_tempcredit.
        MODIFY ztint_dp_tmct_h FROM ls_ztint_dp_tmct_h.
        MODIFY ztint_dp_tmct_i FROM TABLE lt_ztint_dp_tmct_i.
        "锁表
        CALL FUNCTION 'DEQUEUE_EZ_INT_DP_TMCT_H'
          EXPORTING
            mode_ztint_dp_tmct_h = 'E'
            mandt                = sy-mandt
            instanceid           = lv_instance.

*   推送通知
        IF lt_user_content IS NOT INITIAL.
          lv_title =  '开思助手'.
          lv_msg_type = 'text'.
          CONDENSE lv_url NO-GAPS .

          CALL FUNCTION 'Z_FMQYWX_CASSINT_PUSH_MESSAGE'
            IN UPDATE TASK
            EXPORTING
              iv_title    = lv_title
              iv_msgtype  = lv_msg_type
              iv_url      = lv_url
            TABLES
              it_userlist = lt_user_content.
          COMMIT WORK AND WAIT.
        ENDIF.

        "获取最后审批人信息
*        DATA(lv_notes) = lines( ls_detail-info-sp_record )."多少审批节点
*        DATA(lv_times) = lines( ls_detail-info-sp_record[ lv_notes ]-details )."每个审批节点几个人依次审批
*        DATA(lv_finishuser) = VALUE #( ls_detail-info-sp_record[ lv_notes ]-details[ lv_times ]-approver-userid OPTIONAL ).
*        IF lv_finishuser IS NOT INITIAL.
*          SELECT SINGLE username INTO @DATA(lv_username) FROM ztint_user_inf WHERE qywxuserid = @lv_finishuser.
*        ENDIF.
*        IF lv_username IS INITIAL.
*          lv_username = '企业微信'.
*        ENDIF.
*
*        "获取发起人名称
*        SELECT SINGLE username INTO @DATA(lv_username) FROM ztint_user_inf
*          WHERE qywxuserid = @ls_detail-info-applyer-userid.
*        IF lv_username IS INITIAL.
*          lv_username = '企业微信'.
*        ENDIF.


**          lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

**        CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
**          IN UPDATE TASK
**          EXPORTING
**            iv_content = lv_alert_content
**            iv_fname   = lv_fname.
**
**        CALL FUNCTION 'Z_FMQYWX_CASSINT_PUSH_MESSAGE'
**          IN UPDATE TASK
**          EXPORTING
**            iv_title    = lv_title
**            iv_msg_type = lv_msg_type
**            iv_url      = lv_url
**          TABLES
**            it_userlist = lt_user_content.
      WHEN 'refuse'."审批拒绝
        CLEAR ls_ztint_tempcredit.
        SELECT SINGLE * INTO ls_ztint_tempcredit FROM ztint_tempcredit WHERE instaceid = iv_spno.
        IF sy-subrc = 0.
*-LONGLF ADD IN 20210617
          NEW zcl_icec_finance_api( )->tempcredit_approval_syn(
              EXPORTING is_ztint_tempcredit = ls_ztint_tempcredit
                        is_detail_qywx = ls_detail
                        iv_qywx = 'X'
                        iv_type = iv_type
              IMPORTING es_res = ls_respon ).
          IF ls_respon-code NE '200'.
            TRY .
                ls_ztint_dp_tmct_l-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
              CATCH cx_uuid_error.
            ENDTRY.

            ls_ztint_dp_tmct_l-event_desc = '临时额度审批状态同步金融'.
            ls_ztint_dp_tmct_l-event_id = 'tempcredit_approval_syn'.
            ls_ztint_dp_tmct_l-status = 'E'.
            ls_ztint_dp_tmct_l-message = ls_respon-desc.
            ls_ztint_dp_tmct_l-key_value1 = iv_spno.
            ls_ztint_dp_tmct_l-key_value2 = lv_code.
            ls_ztint_dp_tmct_l-zcrt_bdate = sy-datum.
            ls_ztint_dp_tmct_l-zcrt_btime = sy-uzeit.
            MODIFY ztint_dp_tmct_l FROM ls_ztint_dp_tmct_l.
            CLEAR ls_ztint_dp_tmct_l.
          ENDIF.
*-END OF ADD
          TRY .
              ls_ztint_dp_tmct_l-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
            CATCH cx_uuid_error.
          ENDTRY.

          ls_ztint_dp_tmct_l-event_desc = '更新临时额度申请状态'.
          ls_ztint_dp_tmct_l-event_id = 'tempcredit_refuse_status'.
          ls_ztint_dp_tmct_l-status = 'S'.
          ls_ztint_dp_tmct_l-message = '更新临时额度申请状态'.
          ls_ztint_dp_tmct_l-key_value1 = iv_spno.
          ls_ztint_dp_tmct_l-key_value2 = lv_code.
          ls_ztint_dp_tmct_l-zcrt_bdate = sy-datum.
          ls_ztint_dp_tmct_l-zcrt_btime = sy-uzeit.
          MODIFY ztint_dp_tmct_l FROM ls_ztint_dp_tmct_l.

          lv_creditapply = ls_ztint_tempcredit-creditapply / 10000.
          IF ls_ztint_tempcredit-isspcreditapply EQ 'Y'.
            SELECT SINGLE * INTO @ls_spcredit FROM ztint_spcredit WHERE yearmonth = @ls_ztint_tempcredit-zcrt_bdate+0(6)
              AND deptid = @ls_ztint_tempcredit-spcreditdeptid.
            IF sy-subrc EQ 0.
              ls_spcredit = VALUE #( BASE ls_spcredit freezecredit = ls_spcredit-freezecredit - lv_creditapply
                                     availablecredit = ls_spcredit-availablecredit + lv_creditapply
                                     zupd_bdate = sy-datum zupd_btime = sy-uzeit zupd_uname = '钉钉' ).
              MODIFY ztint_spcredit FROM ls_spcredit.
            ENDIF.
          ENDIF.

          ls_ztint_tempcredit-status = 'UNAPPROVE'."审批拒绝
          ls_ztint_tempcredit-msg = '审批拒绝'.
          ls_ztint_tempcredit-zupd_bdate = sy-datum.
          ls_ztint_tempcredit-zupd_btime = sy-uzeit.
          ls_ztint_tempcredit-zupd_uname = '企业微信'.

          MODIFY ztint_tempcredit FROM ls_ztint_tempcredit.

        ELSE.
          TRY .
              ls_ztint_dp_tmct_l-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
            CATCH cx_uuid_error.
          ENDTRY.

          ls_ztint_dp_tmct_l-event_desc = '临时额度审批拒绝'.
          ls_ztint_dp_tmct_l-event_id = 'add_tempcredit_refuse'.
          ls_ztint_dp_tmct_l-status = 'E'.
          ls_ztint_dp_tmct_l-message = '临时额度审批拒绝未找到对应的申请记录'.
          ls_ztint_dp_tmct_l-key_value1 = iv_spno.
          ls_ztint_dp_tmct_l-key_value2 = lv_code.
          ls_ztint_dp_tmct_l-zcrt_bdate = sy-datum.
          ls_ztint_dp_tmct_l-zcrt_btime = sy-uzeit.
          MODIFY ztint_dp_tmct_l FROM ls_ztint_dp_tmct_l.

        ENDIF.
      WHEN 'terminate'."审批撤销
        CLEAR ls_ztint_tempcredit.
        SELECT SINGLE * INTO ls_ztint_tempcredit FROM ztint_tempcredit WHERE instaceid = iv_spno.
        IF sy-subrc = 0.
          NEW zcl_icec_finance_api( )->tempcredit_approval_syn(
              EXPORTING is_ztint_tempcredit = ls_ztint_tempcredit
                        is_detail_qywx = ls_detail
                        iv_qywx = 'X'
                        iv_type = iv_type
              IMPORTING es_res = ls_respon ).
          IF ls_respon-code NE '200'.
            TRY .
                ls_ztint_dp_tmct_l-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
              CATCH cx_uuid_error.
            ENDTRY.

            ls_ztint_dp_tmct_l-event_desc = '临时额度审批状态同步金融'.
            ls_ztint_dp_tmct_l-event_id = 'tempcredit_approval_syn'.
            ls_ztint_dp_tmct_l-status = 'E'.
            ls_ztint_dp_tmct_l-message = ls_respon-desc.
            ls_ztint_dp_tmct_l-key_value1 = iv_spno.
            ls_ztint_dp_tmct_l-key_value2 = lv_code.
            ls_ztint_dp_tmct_l-zcrt_bdate = sy-datum.
            ls_ztint_dp_tmct_l-zcrt_btime = sy-uzeit.
            MODIFY ztint_dp_tmct_l FROM ls_ztint_dp_tmct_l.
            CLEAR ls_ztint_dp_tmct_l.
          ENDIF.
          TRY .
              ls_ztint_dp_tmct_l-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
            CATCH cx_uuid_error.
          ENDTRY.

          ls_ztint_dp_tmct_l-event_desc = '更新临时额度申请撤销'.
          ls_ztint_dp_tmct_l-event_id = 'tempcredit_terminate_status'.
          ls_ztint_dp_tmct_l-status = 'S'.
          ls_ztint_dp_tmct_l-message = '更新临时额度申请状态撤销'.
          ls_ztint_dp_tmct_l-key_value1 = iv_spno.
          ls_ztint_dp_tmct_l-key_value2 = lv_code.
          ls_ztint_dp_tmct_l-zcrt_bdate = sy-datum.
          ls_ztint_dp_tmct_l-zcrt_btime = sy-uzeit.
          MODIFY ztint_dp_tmct_l FROM ls_ztint_dp_tmct_l.

          ls_ztint_tempcredit-status = 'CANCELED'."审批撤销
          ls_ztint_tempcredit-msg = '已撤销'.
          ls_ztint_tempcredit-zupd_bdate = sy-datum.
          ls_ztint_tempcredit-zupd_btime = sy-uzeit.
          ls_ztint_tempcredit-zupd_uname = '钉钉'.
          MODIFY ztint_tempcredit FROM ls_ztint_tempcredit.

          lv_creditapply = ls_ztint_tempcredit-creditapply / 10000.
          IF ls_ztint_tempcredit-isspcreditapply EQ 'Y'.
            SELECT SINGLE * INTO @ls_spcredit FROM ztint_spcredit WHERE yearmonth = @ls_ztint_tempcredit-zcrt_bdate+0(6)
              AND deptid = @ls_ztint_tempcredit-spcreditdeptid.
            IF sy-subrc EQ 0.
              ls_spcredit = VALUE #( BASE ls_spcredit freezecredit = ls_spcredit-freezecredit - lv_creditapply
                                     availablecredit = ls_spcredit-availablecredit + lv_creditapply
                                     zupd_bdate = sy-datum zupd_btime = sy-uzeit zupd_uname = '钉钉' ).
              MODIFY ztint_spcredit FROM ls_spcredit.
            ENDIF.
          ENDIF.
        ELSE.
          TRY .
              ls_ztint_dp_tmct_l-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
            CATCH cx_uuid_error.
          ENDTRY.

          ls_ztint_dp_tmct_l-event_desc = '临时额度审批拒绝'.
          ls_ztint_dp_tmct_l-event_id = 'add_tempcredit_terminate'.
          ls_ztint_dp_tmct_l-status = 'E'.
          ls_ztint_dp_tmct_l-message = '临时额度审批拒绝未找到对应的申请记录'.
          ls_ztint_dp_tmct_l-key_value1 = iv_spno.
          ls_ztint_dp_tmct_l-key_value2 = lv_code.
          ls_ztint_dp_tmct_l-zcrt_bdate = sy-datum.
          ls_ztint_dp_tmct_l-zcrt_btime = sy-uzeit.
          MODIFY ztint_dp_tmct_l FROM ls_ztint_dp_tmct_l.
        ENDIF.
      WHEN OTHERS.
    ENDCASE.
    "审批左后节点有=备注信息推送消息至提交人
    CLEAR ls_user_content. REFRESH lt_user_content.
    DATA lv_url1  TYPE string.
    DATA lv_substr  TYPE string.
    DATA lv_speech  TYPE string.
    DATA(lv_lines) = lines( ls_detail-info-sp_record ).
    READ TABLE ls_detail-info-sp_record INTO DATA(ls_record) INDEX lv_lines.
    LOOP AT ls_record-details INTO DATA(ls_recorddetail) WHERE speech NE ''.
      lv_speech = COND #( WHEN lv_speech IS INITIAL THEN ls_recorddetail-speech ELSE |{ lv_speech };{ ls_recorddetail-speech }| ).
    ENDLOOP.
    DATA lv_wxcontent(512) TYPE c.
    IF iv_type EQ 'agree' OR  iv_type EQ 'refuse' AND  ls_detail-info-comments IS NOT INITIAL.
      ls_tempcredit_data-creditapply = ls_tempcredit_data-creditapply / 10000.
      DATA(lv_status1) = COND #( WHEN iv_type EQ 'agree' THEN '已通过' ELSE '已驳回' ).
      ls_user_content-userid = ls_tempcredit_data-originatoruserid.
      ls_user_content-orderno = ls_tempcredit_data-orderid.
      ls_user_content-orderdesc = ls_tempcredit_data-companycode.
      ls_user_content-firstcatgory = '临时额度申请'.
      ls_user_content-secondcatgory ='临时额度申请审批消息通知'.
      lv_wxcontent = |<div>你的客户临时额度申请{ lv_status1 } </div>| &&
                                  |<div class=\\"highlight\\">审批编号：{ iv_spno }</div>| &&
                                  |<div>客户名称：{ ls_tempcredit_data-companyname }</div>| &&
                                  |<div>审批额度：{ ls_tempcredit_data-creditapply }万</div>| &&
                                  |<div>审批意见：{ lv_speech }</div>| &&
                                  |<div>时间：{ sy-datum+0(4) }年{ sy-datum+4(2) }月{ sy-datum+6(2) }日 { sy-uzeit TIME = ISO }</div>| .
      CALL METHOD cl_abap_list_utilities=>dynamic_output_length( EXPORTING field = lv_content RECEIVING len = DATA(lv_len) ).
      IF lv_len > 512.
        DATA(lv_len1) = strlen( lv_content ).
        DATA(lv_doublenum) = lv_len - lv_len1.
        DATA(lv_single) = lv_len - lv_doublenum.
        DATA(lv_double) = lv_doublenum * 2.
        IF lv_double > 506.
          lv_wxcontent = |{ lv_wxcontent+0(253) }</div>|.
        ELSE.
          DATA(lv_len2) =  ( 506 - lv_double ) + lv_doublenum.
          lv_wxcontent = |{ lv_wxcontent+0(lv_len2) }</div>|.
        ENDIF.
      ENDIF.
      ls_user_content-wxcontent = lv_wxcontent.
      APPEND ls_user_content TO lt_user_content.
      IF lt_user_content IS NOT INITIAL.
        lv_title = '开思助手消息中心'.
        lv_msg_type = 'action_card'.
        lv_url1 =  '#/moneyApply/detail/' && ls_tempcredit_data-guid  && '?isMessagePush=true' ."临时额度详情
        CALL FUNCTION 'Z_FMINT_WO_PUSH'
          IN UPDATE TASK
          EXPORTING
            iv_title    = lv_title
            iv_msg_type = lv_msg_type
            iv_url      = lv_url1
          TABLES
            it_userlist = lt_user_content.
        COMMIT WORK AND WAIT.
      ENDIF.
    ENDIF.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPLY_AFTERSALE_REFUSE
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
METHOD apply_aftersale_refuse.

  DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h,
        ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
        lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i,
        ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
        lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l,
        ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
  DATA:lt_user_content TYPE  zsint_push_userlist_tab,
       ls_user_content TYPE  zsint_push_userlist.
  DATA:lv_body          TYPE string,
       lv_instance      TYPE zde_intprocessinstanceid,
       lv_alert_content TYPE string,
       lv_fname         TYPE string,
       lv_url           TYPE string,
       lv_title         TYPE string,
       lv_msg_type      TYPE string,
       lv_content       TYPE string,
       lv_date_s        TYPE string.
 DATA: lv_Isbackup TYPE ztint_afrefuse-Isbackup,"市场是否客情兜底(X 是）
       lv_Isbackupdesc TYPE ztint_afrefuse-Isbackupdesc,"
       lv_Estimatedamount type ztint_afrefuse-Estimatedamount."预估客情兜底金额

  DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
  DATA(lv_code) = ls_detail-info-template_id.

  IF ls_detail-errcode NE '0'.
    "没找到 记录日志
    log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
    zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
    sendmsg = 'X' fname = 'BULELIST' ).
    RETURN.
  ENDIF.


  "审批结果
  DATA:lv_status TYPE ztint_afrefuse-status.
  CASE iv_type.
    WHEN 'agree'.
      lv_status = 'APPROVE'.
    WHEN 'refuse'.
      lv_status = 'UNAPPROVE'.
    WHEN 'terminate'.
      lv_status = 'CANCELED'.
    WHEN OTHERS.
  ENDCASE.


  lv_instance = iv_spno.

  SELECT SINGLE guid INTO @DATA(l_guid) FROM ztint_dp_inf WHERE instanceid = @lv_instance.
  CHECK sy-subrc EQ 0.
  "锁表
  CALL FUNCTION 'ENQUEUE_EZ_INT_DP_INF'
    EXPORTING
      mode_ztint_dp_inf = 'E'
      mandt             = sy-mandt
      guid              = l_guid.

  IF sy-subrc <> 0.
    ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_aftersale_refuse'
    eventdesc = '售后拒赔审批流状态更新' status = 'E' message = '更新审批状态锁表失败'
    key1 = iv_spno key2 = lv_code ).
    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
    RETURN.
  ENDIF.

  "更新审批人填写的信息
  LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).

      CASE  ls_content-title[ lang = 'zh_CN' ]-text.
        WHEN '市场是否客情兜底' .
          lv_Isbackup = COND #( WHEN ls_content-value-selector-options[ 1 ]-value[ 1 ]-text EQ '是' THEN 'X' ELSE '' ).
          IF ls_content-value-selector-options[ 1 ]-value[ 1 ]-text NE '待填写'.
            lv_Isbackupdesc = ls_content-value-selector-options[ 1 ]-value[ 1 ]-text.
          ENDIF.

        WHEN '预估客情兜底金额'.
          lv_Estimatedamount = ls_content-value-new_number.
        WHEN OTHERS.
      ENDCASE.

  ENDLOOP.


  SELECT SINGLE * FROM ztint_afrefuse INTO @DATA(ls_refuse) WHERE guid = @l_guid.
  IF sy-subrc NE 0.
    UPDATE ztint_dp_inf
    SET status = lv_status
    msg = 'finish'
    zupd_bdate = sy-datum
    zupd_btime = sy-uzeit
    zupd_uname = sy-uname
    WHERE guid = l_guid.
  ELSE.

    IF iv_spno IS NOT INITIAL AND iv_type EQ 'agree'.
    ELSEIF iv_spno IS NOT INITIAL AND ( iv_type EQ 'refuse'  OR
      iv_type EQ 'terminate' ).


      IF ls_refuse-woid IS NOT INITIAL.
        IF iv_type EQ 'terminate'.
          UPDATE ztint_wo_header SET isrefuse = '' zupd_bdate = sy-datum zupd_btime = sy-uzeit zupd_uname = sy-uname
        WHERE woid = ls_refuse-woid.
        ENDIF.
*        UPDATE ztint_wo_note SET afclaimstaus = lv_status
*        zupd_bdate = sy-datum zupd_btime = sy-uzeit zupd_uname = sy-uname
*        WHERE woid = ls_refuse-woid AND processcode = lv_instance.
      ENDIF.
    ENDIF.

    UPDATE ztint_dp_inf
    SET status = lv_status
    msg = 'finish'
    zupd_bdate = sy-datum
    zupd_btime = sy-uzeit
    zupd_uname = sy-uname
    WHERE guid = l_guid.

    UPDATE ztint_afrefuse
    SET status = lv_status
    Isbackup = lv_Isbackup
    Isbackupdesc = lv_Isbackupdesc
    Estimatedamount = lv_Estimatedamount
    msg = 'finish'
    businessid = iv_spno
    zupd_bdate = sy-datum
    zupd_btime = sy-uzeit
    zupd_uname = sy-uname
    WHERE guid = l_guid.


  ENDIF.

  CALL FUNCTION 'DEQUEUE_EZ_INT_DP_INF'
    EXPORTING
      mode_ztint_dp_inf = 'E'
      mandt             = sy-mandt
      guid              = l_guid.
ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPLY_COUPON
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD apply_coupon.

    DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h,
          ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
          lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i,
          ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
          lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l,
          ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
    DATA:lt_user_content TYPE  zsint_push_userlist_tab,
         ls_user_content TYPE  zsint_push_userlist.
    DATA:lv_body          TYPE string,
         lv_instance      TYPE zde_intprocessinstanceid,
         lv_alert_content TYPE string,
         lv_fname         TYPE string,
         lv_url           TYPE string,
         lv_title         TYPE string,
         lv_msg_type      TYPE string,
         lv_content       TYPE string,
         lv_date_s        TYPE string.

    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
    DATA(lv_code) = ls_detail-info-template_id.

    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
                           sendmsg = 'X' fname = 'COUPON' ).
      RETURN.
    ENDIF.

    "审批结果
    CASE iv_type.
      WHEN 'agree'. "审批通过
        "查找申请记录
        SELECT SINGLE * FROM ztint_cp_list INTO @DATA(ls_ztint_cp_list) WHERE instanceid = @iv_spno.
        IF sy-subrc NE 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_coupon'
                    eventdesc = '申请优惠券发放' status = 'E' message = '未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.
        "判断是否已经处理过
        SELECT SINGLE * FROM ztint_dp_pi_h INTO CORRESPONDING FIELDS OF @ls_ztint_dp_pi_h
          WHERE processinstanceid = @iv_spno.
        IF ls_ztint_dp_pi_h-sendstatus = 'Y'. "处理过
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_coupon'
                    eventdesc = '申请优惠券发放' status = 'E' message = '实例优惠券申请发放已处理过'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.
        lv_instance = iv_spno.
        "锁表
        CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.
        IF sy-subrc <> 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_coupon'
                    eventdesc = '申请优惠券发放' status = 'E' message = '实例优惠券申请发放锁表失败'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.

        "抬头信息
        ls_ztint_dp_pi_h = prepare_ztint_dp_pi_h( is_detail = ls_detail iv_type = iv_type ).
        "审批详情
        LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
          CLEAR ls_ztint_dp_pi_i.
          ls_ztint_dp_pi_i = prepare_ztint_dp_pi_i( iv_spno = iv_spno is_content = ls_content ).
          APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.
        ENDLOOP.

        "获取最后审批人信息
        DATA(lv_notes) = lines( ls_detail-info-sp_record )."多少审批节点
*        DATA(lv_times) = lines( ls_detail-info-sp_record[ lv_notes ]-details )."每个审批节点几个人依次审批
*        DATA(lv_finishuser) = VALUE #( ls_detail-info-sp_record[ lv_notes ]-details[ lv_times ]-approver-userid OPTIONAL ).
        DATA(lt_time) = ls_detail-info-sp_record[ lv_notes ]-details."每个审批节点几个人依次审批
        SORT lt_time BY sptime DESCENDING.
        READ TABLE lt_time INTO DATA(ls_time) INDEX 1.
        DATA(lv_finishuser)  = ls_time-approver-userid.
        IF lv_finishuser IS NOT INITIAL.
          SELECT SINGLE userid,username INTO @DATA(ls_opera_per) FROM ztint_user_inf WHERE qywxuserid = @lv_finishuser.
        ENDIF.

        DATA(lo_coupon) = NEW zcl_icec_coupon_api( ).
        lo_coupon->send_applid_coupon_batch(
            EXPORTING iv_cpid = EXACT string( ls_ztint_cp_list-cpid )
                      iv_companyid = EXACT string( ls_ztint_cp_list-companyid )
                      iv_num = EXACT string( ls_ztint_cp_list-znum )
            IMPORTING es_msg = DATA(ls_msg)
                      es_return = DATA(ls_return) ).
        IF ls_return-code EQ '200'.
          "审批抬头表
          ls_ztint_dp_pi_h-sendstatus = 'Y'."已处理过
          ls_ztint_dp_pi_h-sendmsg = '成功'.
          "更新优惠券申请记录
          UPDATE ztint_cp_list SET status = 'APPROVE'
                                   state = '已发放'
                                   zcrtbdate = sy-datum
                                   zcrtbtime = sy-uzeit
                                   zupd_bdate = sy-datum
                                   zupd_btime = sy-uzeit
                                   zupd_uname = sy-uname
                   WHERE guid = ls_ztint_cp_list-guid.
        ELSE.
          "审批抬头表
          ls_ztint_dp_pi_h-sendstatus = 'Y'."已处理过
          ls_ztint_dp_pi_h-sendmsg = '发放失败'.
          "更新优惠券申请记录
          UPDATE ztint_cp_list SET status = 'APPROVE'
                                   state = '发放失败'
                                   msg = ls_return-msg
                                   zupd_bdate = sy-datum
                                   zupd_btime = sy-uzeit
                                   zupd_uname = sy-uname
                   WHERE guid = ls_ztint_cp_list-guid.
          SELECT SINGLE * FROM ztint_cp_count INTO @DATA(ls_count) WHERE modelid = @ls_ztint_cp_list-modelid AND cpid = @ls_ztint_cp_list-cpid.
          IF sy-subrc EQ 0.
            ls_count-sendnumber =   ls_count-sendnumber - ls_ztint_cp_list-znum.
            IF ls_count-sendnumber <= 0.
              ls_count-sendnumber = 0.
            ENDIF.
            ls_count-zupd_bdate = sy-datum.
            ls_count-zupd_btime = sy-uzeit.
            ls_count-zupd_uname = sy-uname.
            MODIFY ztint_cp_count FROM ls_count.
          ENDIF.
        ENDIF.

*****记录操作日志
        IF ls_return-code EQ '200'.
          lv_content = |{ ls_opera_per-username }已批准({ ls_ztint_cp_list-username })的优惠券申请发放且发放成功|.
        ELSE.
          lv_content = |{ ls_opera_per-username }已批准({ ls_ztint_cp_list-username })的优惠券申请发放,| &&
                       |但由于{ ls_return-msg }发放失败|.
        ENDIF.
        TRY.
            DATA(ls_opera_log) = VALUE ztint_opera_log( guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
                                                        fnam = 'M008' operatetion = 'O001' userid = ls_ztint_cp_list-userid
                                                        username = ls_ztint_cp_list-username objectid = ls_opera_per-username
                                                        content = lv_content operatedate = sy-datum operatetime = sy-uzeit ).
            MODIFY ztint_opera_log FROM ls_opera_log.
          CATCH cx_uuid_error.
        ENDTRY.
        MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.
        MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i.
        "解锁
        CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.

        IF ls_return-code NE '200'."通知发起人
          IF ls_return-msg IS INITIAL.
            ls_return-msg = '优惠券发放失败'.
          ENDIF.
          DATA:lv_subtitle TYPE string.
          ls_user_content-userid = ls_ztint_cp_list-userid.
          ls_user_content-content = ' 开思助手'.
          lv_subtitle = '<font color=#DC143C> **您申请的优惠券发放失败，敬请关注!** </font> '.
          ls_user_content-content = ls_user_content-content && '  \n  ' && lv_subtitle.
          ls_user_content-content = ls_user_content-content && '  \n  ' && '优惠券名称:' && ls_ztint_cp_list-zdescribe.
          ls_user_content-content = ls_user_content-content && '  \n  ' && '优惠券描述:' && ls_ztint_cp_list-reach.
          ls_user_content-content = ls_user_content-content && '  \n  ' && '维修厂:' && ls_ztint_cp_list-cpwxc.
          ls_user_content-content = ls_user_content-content && '  \n  ' && '失败原因:' && ls_return-msg.
          ls_user_content-content = ls_user_content-content && '  \n  ' && '时间:' && |{ sy-datum DATE = ISO }{ sy-uzeit TIME = ISO }|.

          ls_user_content-wxcontent = |<div class=\\"highlight\\">您申请的优惠券发放失败，敬请关注!</div>| &&
                                      |<div class=\\"gray\\">{ sy-datum DATE = ISO }{ sy-uzeit TIME = ISO }</div>| &&
                                      | | &&
                                      |<div>优惠券名称:{ ls_ztint_cp_list-zdescribe }</div>| &&
                                      |<div>优惠券描述:{ ls_ztint_cp_list-reach }</div>| &&
                                      |<div>维修厂:{ ls_ztint_cp_list-cpwxc }</div>| &&
                                      |<div>失败原因:{ ls_return-msg }</div>|.

          ls_user_content-orderno = |{ ls_ztint_cp_list-companyid }|.
          ls_user_content-orderdesc = |{ ls_ztint_cp_list-cpwxc }|.
          ls_user_content-firstcatgory = '客户管理'.
          ls_user_content-secondcatgory = '优惠券发放申请审批'.
          APPEND ls_user_content TO lt_user_content.
        ENDIF.

      WHEN 'refuse'."审批拒绝
        CLEAR ls_ztint_cp_list.
        SELECT SINGLE * FROM ztint_cp_list INTO CORRESPONDING FIELDS OF ls_ztint_cp_list WHERE instanceid = iv_spno.
        IF sy-subrc = 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_coupon'
                    eventdesc = '申请优惠券发放' status = 'S' message = '更新优惠券申请发放拒绝状态'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.

          "获取最后审批人信息
          CLEAR:lv_notes,lt_time,ls_time,lv_finishuser.
          lv_notes = lines( ls_detail-info-sp_record )."多少审批节点
*          lv_times = lines( ls_detail-info-sp_record[ lv_notes ]-details )."每个审批节点几个人依次审批
*          lv_finishuser = VALUE #( ls_detail-info-sp_record[ lv_notes ]-details[ lv_times ]-approver-userid OPTIONAL ).
          lt_time = ls_detail-info-sp_record[ lv_notes ]-details."每个审批节点几个人依次审批
          SORT lt_time BY sptime DESCENDING.
          READ TABLE lt_time INTO ls_time INDEX 1.
          lv_finishuser = ls_time-approver-userid.
          IF lv_finishuser IS NOT INITIAL.
            SELECT SINGLE username INTO @DATA(lv_username) FROM ztint_user_inf WHERE qywxuserid = @lv_finishuser.
          ENDIF.
          IF lv_username IS INITIAL.
            lv_username = '企业微信'.
          ENDIF.
          DATA(lv_unapprmsg) = lv_username && '已拒绝该审批'.
          UPDATE ztint_cp_list SET status = 'UNAPPROVE'
                                      msg = lv_unapprmsg
                               zupd_bdate = sy-datum
                               zupd_btime = sy-uzeit
                               zupd_uname = sy-uname
               WHERE guid = ls_ztint_cp_list-guid.

          SELECT SINGLE * FROM ztint_cp_count INTO @ls_count WHERE modelid = @ls_ztint_cp_list-modelid AND cpid = @ls_ztint_cp_list-cpid.
          IF sy-subrc EQ 0.
            ls_count-sendnumber =   ls_count-sendnumber - ls_ztint_cp_list-znum.
            IF ls_count-sendnumber <= 0.
              ls_count-sendnumber = 0.
            ENDIF.
            ls_count-zupd_bdate = sy-datum.
            ls_count-zupd_btime = sy-uzeit.
            ls_count-zupd_uname = sy-uname.
            MODIFY ztint_cp_count FROM ls_count.
          ENDIF.

*****记录操作日志
          lv_content = |{ lv_username }已拒绝({ ls_ztint_cp_list-username })的优惠券申请发放|.
          TRY .
              ls_opera_log = VALUE ztint_opera_log( guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
                                                          fnam = 'M008' operatetion = 'O001' userid = ls_ztint_cp_list-userid
                                                          username = ls_ztint_cp_list-username objectid = lv_username
                                                          content = lv_content operatedate = sy-datum operatetime = sy-uzeit ).
              MODIFY ztint_opera_log FROM ls_opera_log.
            CATCH cx_uuid_error.
          ENDTRY.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_coupon'
                    eventdesc = '申请优惠券发放' status = 'E' message = '更新优惠券申请发放拒绝状态未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ENDIF.
      WHEN 'terminate'."审批撤销
        SELECT SINGLE * FROM ztint_cp_list INTO CORRESPONDING FIELDS OF ls_ztint_cp_list
       WHERE instanceid = iv_spno.
        IF sy-subrc = 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_coupon'
                    eventdesc = '申请优惠券发放' status = 'S' message = '更新优惠券申请发放撤销'
                    key1 = iv_spno key2 = lv_code ).
          UPDATE ztint_cp_list SET status = 'CANCELED'
                                  msg = '已撤销'
                                  zupd_bdate = sy-datum
                                  zupd_btime = sy-uzeit
                                  zupd_uname = sy-uname
                   WHERE guid = ls_ztint_cp_list-guid.
          SELECT SINGLE * FROM ztint_cp_count INTO @ls_count WHERE modelid = @ls_ztint_cp_list-modelid AND cpid = @ls_ztint_cp_list-cpid.
          IF sy-subrc EQ 0.
            ls_count-sendnumber =   ls_count-sendnumber - ls_ztint_cp_list-znum.
            IF ls_count-sendnumber <= 0.
              ls_count-sendnumber = 0.
            ENDIF.
            ls_count-zupd_bdate = sy-datum.
            ls_count-zupd_btime = sy-uzeit.
            ls_count-zupd_uname = sy-uname.
            MODIFY ztint_cp_count FROM ls_count.
          ENDIF.
*****记录操作日志
          lv_content = |{ ls_ztint_cp_list-username }已撤销优惠券申请发放|.
          TRY .
              ls_opera_log = VALUE ztint_opera_log( guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
                                                          fnam = 'M008' operatetion = 'O001' userid = ls_ztint_cp_list-userid
                                                          username = ls_ztint_cp_list-username objectid = ls_ztint_cp_list-guid
                                                          content = lv_content operatedate = sy-datum operatetime = sy-uzeit ).
              MODIFY ztint_opera_log FROM ls_opera_log.
            CATCH cx_uuid_error.
          ENDTRY.
        ELSE.

          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_coupon'
                    eventdesc = '申请优惠券发放' status = 'E' message = '优惠券申请发放撤销未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ENDIF.
      WHEN OTHERS.
    ENDCASE.
    "推送提醒
    IF lt_user_content IS NOT INITIAL.
      lv_msg_type = 'textcard'.
      lv_title =  '开思助手优惠券申请发放'.

      CALL FUNCTION 'Z_FMQYWX_CASSINT_PUSH_MESSAGE'
        IN UPDATE TASK
        EXPORTING
          iv_title    = lv_title
          iv_msgtype  = lv_msg_type
          iv_url      = lv_url
        TABLES
          it_userlist = lt_user_content.
    ENDIF.

  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPLY_COUPON_ADDAMOUNT
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD apply_coupon_addamount.

    DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h,
          ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
          lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i,
          ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
          lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l,
          ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
    DATA:lt_user_content TYPE  zsint_push_userlist_tab,
         ls_user_content TYPE  zsint_push_userlist.
    DATA:lv_body          TYPE string,
         lv_instance      TYPE zde_intprocessinstanceid,
         lv_alert_content TYPE string,
         lv_fname         TYPE string,
         lv_url           TYPE string,
         lv_title         TYPE string,
         lv_msg_type      TYPE string,
         lv_content       TYPE string,
         lv_date_s        TYPE string.

    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
    DATA(lv_code) = ls_detail-info-template_id.

    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
                           sendmsg = 'X' fname = 'COUPONADD' ).
      RETURN.
    ENDIF.


    "审批结果
    CASE iv_type.
      WHEN 'agree'. "审批通过
        "查找申请记录
        SELECT SINGLE * FROM ztint_cp_add INTO @DATA(ls_ztint_cp_add)
          WHERE instanceid = @iv_spno.
        IF sy-subrc NE 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_addamount'
                    eventdesc = '加额申请' status = 'E' message = '未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.
        "判断是否已经处理过
        SELECT SINGLE * FROM ztint_dp_pi_h INTO CORRESPONDING FIELDS OF @ls_ztint_dp_pi_h
          WHERE processinstanceid = @iv_spno.
        IF ls_ztint_dp_pi_h-sendstatus = 'Y'. "处理过
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_addamount'
                    eventdesc = '加额申请' status = 'E' message = '实例加额申请已处理过'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.

        lv_instance = iv_spno.

        "锁表
        CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.
        IF sy-subrc <> 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_addamount'
                    eventdesc = '加额申请' status = 'E' message = '实例加额申请锁表失败'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.

        "抬头信息
        ls_ztint_dp_pi_h = prepare_ztint_dp_pi_h( is_detail = ls_detail iv_type = iv_type ).
        "审批详情
        LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
          CLEAR ls_ztint_dp_pi_i.
          ls_ztint_dp_pi_i = prepare_ztint_dp_pi_i( iv_spno = iv_spno is_content = ls_content ).
          APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.
        ENDLOOP.

        "获取最后审批人信息
        DATA(lv_notes) = lines( ls_detail-info-sp_record )."多少审批节点
**        DATA(lv_times) = lines( ls_detail-info-sp_record[ lv_notes ]-details )."每个审批节点几个人依次审批
**        DATA(lv_finishuser) = VALUE #( ls_detail-info-sp_record[ lv_notes ]-details[ lv_times ]-approver-userid OPTIONAL ).
        DATA(lt_time) = ls_detail-info-sp_record[ lv_notes ]-details."每个审批节点几个人依次审批
        SORT lt_time BY sptime DESCENDING.
        READ TABLE lt_time INTO DATA(ls_time) INDEX 1.
        DATA(lv_finishuser) = ls_time-approver-userid.
        IF lv_finishuser IS NOT INITIAL.
          SELECT SINGLE username INTO @DATA(lv_username) FROM ztint_user_inf WHERE qywxuserid = @lv_finishuser.
        ENDIF.
        IF lv_username IS INITIAL.
          lv_username = '企业微信'.
        ENDIF.

        "审批抬头表
        ls_ztint_dp_pi_h-sendstatus = 'Y'."已处理过
        ls_ztint_dp_pi_h-sendmsg = '成功'.

        "更新商家准入申请记录
        UPDATE ztint_cp_add SET state = 'T'
                                  zupd_bdate = sy-datum
                                  zupd_btime = sy-uzeit
                                  zupd_uname = sy-uname
                 WHERE guid = ls_ztint_cp_add-guid.

        "更新审批抬头表
        UPDATE ztint_cp_note SET state = 'T'
                                 spyj =  '审批通过'
                                 zupd_bdate = sy-datum
                                 zupd_btime = sy-uzeit
                                 zupd_uname = sy-uname
                WHERE guid = ls_ztint_cp_add-guid
                  AND zlevel = ls_ztint_cp_add-zlevel.
        DATA(lv_month) = sy-datum+0(6).
        SELECT SINGLE *  INTO @DATA(ls_ztint_coupon) FROM  ztint_coupon
                WHERE userid = @ls_ztint_cp_add-userid
                  AND zmonth = @lv_month.
        IF sy-subrc = 0.
          ls_ztint_coupon-addamout = ls_ztint_coupon-addamout + ls_ztint_cp_add-addamount.
          MODIFY ztint_coupon FROM ls_ztint_coupon.
        ELSE.
          ls_ztint_coupon-userid = ls_ztint_cp_add-userid.
          ls_ztint_coupon-zmonth = sy-datum+0(6).
          ls_ztint_coupon-name = ls_ztint_cp_add-uname.
          ls_ztint_coupon-telf = ls_ztint_cp_add-telf.
          ls_ztint_coupon-addamout = ls_ztint_cp_add-addamount.
          MODIFY ztint_coupon FROM ls_ztint_coupon.
        ENDIF.

*****记录操作日志
        lv_content = |{ lv_username }已批准({ ls_ztint_cp_add-uname })的加额申请|.
        TRY .
            DATA(ls_opera_log) = VALUE ztint_opera_log( guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
                                                        fnam = 'M008' operatetion = 'O001' userid = ls_ztint_cp_add-userid
                                                        username = ls_ztint_cp_add-uname objectid = lv_username
                                                        content = lv_content operatedate = sy-datum operatetime = sy-uzeit ).
            MODIFY ztint_opera_log FROM ls_opera_log.
          CATCH cx_uuid_error.
        ENDTRY.



        MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i.

        "解锁
        CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.
      WHEN 'refuse'."审批拒绝
        CLEAR ls_ztint_cp_add.
        SELECT SINGLE * FROM ztint_cp_add INTO CORRESPONDING FIELDS OF ls_ztint_cp_add WHERE instanceid = iv_spno.
        IF sy-subrc = 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_addamount'
                    eventdesc = '加额申请' status = 'S' message = '更新加额申请拒绝状态'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.

          UPDATE ztint_cp_add SET state = 'J'
                                zupd_bdate = sy-datum
                                 zupd_btime = sy-uzeit
                                 zupd_uname = sy-uname
                WHERE guid = ls_ztint_cp_add-guid.

          "更新审批抬头表
          UPDATE ztint_cp_note SET state = 'J'
                                    spyj =  '审批已被拒绝'
                                    zupd_bdate = sy-datum
                                    zupd_btime = sy-uzeit
                                    zupd_uname = sy-uname
                   WHERE guid = ls_ztint_cp_add-guid
                     AND zlevel = ls_ztint_cp_add-zlevel.

          CLEAR:lv_notes,lt_time,ls_time,lv_finishuser.
          "获取最后审批人信息
          lv_notes = lines( ls_detail-info-sp_record )."多少审批节点
*          lv_times = lines( ls_detail-info-sp_record[ lv_notes ]-details )."每个审批节点几个人依次审批
*          lv_finishuser = VALUE #( ls_detail-info-sp_record[ lv_notes ]-details[ lv_times ]-approver-userid OPTIONAL ).
          lt_time = ls_detail-info-sp_record[ lv_notes ]-details."每个审批节点几个人依次审批
          SORT lt_time BY sptime DESCENDING.
          READ TABLE lt_time INTO ls_time INDEX 1.
          lv_finishuser = ls_time-approver-userid.
          IF lv_finishuser IS NOT INITIAL.
            SELECT SINGLE username INTO @lv_username FROM ztint_user_inf WHERE qywxuserid = @lv_finishuser.
          ENDIF.
          IF lv_username IS INITIAL.
            lv_username = '企业微信'.
          ENDIF.
*****记录操作日志
          lv_content = |{ lv_username }已拒绝({ ls_ztint_cp_add-uname })的加额申请|.
          TRY .
              ls_opera_log = VALUE ztint_opera_log( guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
                                                          fnam = 'M008' operatetion = 'O001' userid = ls_ztint_cp_add-userid
                                                          username = ls_ztint_cp_add-uname objectid = lv_username
                                                          content = lv_content operatedate = sy-datum operatetime = sy-uzeit ).
              MODIFY ztint_opera_log FROM ls_opera_log.
            CATCH cx_uuid_error.
          ENDTRY.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_addamount'
                    eventdesc = '加额申请' status = 'E' message = '更新加额申请拒绝状态未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ENDIF.
      WHEN 'terminate'."审批撤销
        CLEAR ls_ztint_cp_add.
        SELECT SINGLE * FROM ztint_cp_add INTO CORRESPONDING FIELDS OF ls_ztint_cp_add
          WHERE instanceid = iv_spno.
        IF sy-subrc = 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_addamount'
                    eventdesc = '加额申请' status = 'S' message = '更新加额申请撤销'
                    key1 = iv_spno key2 = lv_code ).

          UPDATE ztint_cp_add SET state = 'C'
                                    zupd_bdate = sy-datum
                                    zupd_btime = sy-uzeit
                                    zupd_uname = sy-uname
                   WHERE guid = ls_ztint_cp_add-guid.

          "更新审批抬头表
          UPDATE ztint_cp_note SET state = 'C'
                                    zupd_bdate = sy-datum
                                    zupd_btime = sy-uzeit
                                    zupd_uname = sy-uname
                   WHERE guid = ls_ztint_cp_add-guid
                     AND zlevel = ls_ztint_cp_add-zlevel.

*****记录操作日志
          lv_content = |{ ls_ztint_cp_add-uname }已撤销加额申请|.
          TRY .
              ls_opera_log = VALUE ztint_opera_log( guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
                                                   fnam = 'M008' operatetion = 'O001' userid = ls_ztint_cp_add-userid
                                                   username = ls_ztint_cp_add-uname objectid = ls_ztint_cp_add-guid
                                                   content = lv_content operatedate = sy-datum operatetime = sy-uzeit ).
              MODIFY ztint_opera_log FROM ls_opera_log.
            CATCH cx_uuid_error.
          ENDTRY.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_addamount'
                    eventdesc = '加额申请' status = 'E' message = '加额申请撤销未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ENDIF.
      WHEN OTHERS.
    ENDCASE.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPLY_ICEC_COUPON_ISSUE_HQ
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* | [--->] IV_CODE                        TYPE        STRING(optional)
* | [--->] IV_NUMBER                      TYPE        INT4(optional)
* | [--->] IS_DETAIL                      TYPE        TS_APPROVALDETAIL(optional)
* | [<---] IS_DP_PI_H                     TYPE        ZTINT_DP_PI_H
* | [<---] IV_SUCCESSNUM                  TYPE        INT4
* +--------------------------------------------------------------------------------------</SIGNATURE>
METHOD apply_icec_coupon_issue_hq.

*接口地址：http://ctsp.casstime.com/interface/manage/detail?id=16019&model_id=26268&classify=2716&method=POST&product_id=1
  DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h, "审批发放抬头
        ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
        lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i, "审批流的字段值详情
        ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,

        lt_ztint_dp_pi_s TYPE STANDARD TABLE OF ztint_dp_pi_s, "审批发放明细详情（优惠券、金币）-总部
        ls_ztint_dp_pi_s LIKE LINE OF lt_ztint_dp_pi_s,

        lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l, "审批已审批log
        ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.

  DATA:lt_user_content TYPE  zsint_push_userlist_tab,
       ls_user_content TYPE  zsint_push_userlist.

  DATA:lv_body          TYPE string,
       lv_instance      TYPE zde_intprocessinstanceid,
       lv_alert_content TYPE string,
       lv_fname         TYPE string,
       lv_url           TYPE string,
       lv_title         TYPE string,
       lv_msg_type      TYPE string,
       lv_content       TYPE string,
       lv_date_s        TYPE string.

  DATA: lv_orderamount TYPE int4,
        lv_discount    TYPE int4.

  DATA:lv_desc TYPE string.
  DATA:lv_code TYPE string.
  TYPES:ty_p TYPE p LENGTH 5  DECIMALS 1. "9.8
  DATA lv_successnum TYPE i. "成功发放的优惠券张数

*审批结果 agree 发放
*总部的优惠券发放，要发放的客户清单在ZTINT_HQ_CUSLIST
*本次审批流优惠券模版在ZTINT_COUPON_L
*所选客户本次发放优惠券的状态存放在ztint_dp_pi_s

  IF is_detail IS INITIAL . "开始不发放，后来发放时单独调用一次【二期批量发放使用】
    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).  "审批流的实例单号202503190002
    lv_code = ls_detail-info-template_id. "模版code
    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail_new'
                           zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败'
                           zvalue1 = iv_spno
                           zvalue2 = lv_code
                           sendmsg = 'X'
                           fname = 'BULELIST' ).
      RETURN.
    ENDIF.
  ELSE.
    ls_detail = is_detail .
    lv_code = iv_code .
  ENDIF.

*-判断主表是否有申请记录
  "获取申请记录
  SELECT SINGLE * FROM ztint_market_req
    INTO @DATA(ls_ztint_req)
    WHERE instanceid = @iv_spno.
  IF sy-subrc NE 0.
    "没找到 记录日志
    ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_coupon_hq'
                                              eventdesc = '总部营销-申请模版优惠券'
                                              status = 'E'
                                              message = '未找到对应的申请记录'
                                              key1 = iv_spno
                                              key2 = lv_code ).
    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
    RETURN.
  ENDIF.


*判断是否已经全部发放过
  SELECT SINGLE *
    FROM ztint_dp_pi_h
    INTO CORRESPONDING FIELDS OF @ls_ztint_dp_pi_h
    WHERE processinstanceid = @iv_spno.

  IF ls_ztint_dp_pi_h-sendstatus = 'Y'. "已发放过
    ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_coupon_hq'
                                              eventdesc = '总部营销-申请模版优惠券'
                                              status = 'E'
                                              message = '实例已申请发放过模版优惠券'
                                              key1 = iv_spno
                                              key2 = lv_code ).

    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
    RETURN.
  ENDIF.

  lv_instance = iv_spno.

  "锁表
  CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
    EXPORTING
      mode_ztint_dp_pi_h = 'E'
      mandt              = sy-mandt
      instanceid         = lv_instance.

  IF sy-subrc <> 0.
    ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_coupon_hq'
                                              eventdesc = '总部营销-申请模版优惠券'
                                              status = 'E'
                                              message = '实例模版优惠券锁表失败'
                                              key1 = iv_spno
                                              key2 = lv_code ).

    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
    RETURN.
  ENDIF.

  "抬头信息——这里第一次写抬头H表
  ls_ztint_dp_pi_h = prepare_ztint_dp_pi_h( is_detail = ls_detail iv_type = iv_type ).

  "审批详情
  LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
    CLEAR ls_ztint_dp_pi_i.
    ls_ztint_dp_pi_i = prepare_ztint_dp_pi_i( iv_spno = iv_spno is_content = ls_content ).
    APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.  "这里只是存储，没有像发金币一样动态模版数据
  ENDLOOP.

  "获取发起人名称
  SELECT SINGLE username
    INTO @DATA(lv_username)
    FROM ztint_user_inf
    WHERE qywxuserid = @ls_detail-info-applyer-userid.
  IF lv_username IS INITIAL.
    lv_username = '企业微信'.
  ENDIF.

*客户清单
  SELECT *
    FROM ztint_hq_cuslist
    INTO TABLE @DATA(lt_cuslist)
    WHERE guid = @ls_ztint_req-guid.

*模版清单
  SELECT *
    FROM ztint_coupon_l
    INTO TABLE @DATA(lt_temp)
    WHERE guid = @ls_ztint_req-guid.
  SORT lt_temp BY templateid .

*-已发放的清单
  SELECT *
    FROM ztint_dp_pi_s
    INTO TABLE @DATA(lt_s)
    WHERE guid = @ls_ztint_req-guid.
  SORT lt_s BY companycode templateid .


  DATA:ls_send      TYPE zsint_coupon_send,
       lt_cus_send  TYPE TABLE OF string,
       lt_temp_send TYPE zstint_coupon_list,
       ls_temp_send TYPE zsint_coupon_list.
  DATA:lv_cnt TYPE i  .

*-发放时为了定位哪个客户，采用 一个客户 + 所有模版 的请求模式
*不能采用 所有客户 + 所有模版的请求模式 ，无法定位哪个客户失败了
  LOOP AT lt_cuslist INTO DATA(ls_cus) .
    CLEAR:ls_temp_send,lt_temp_send,lt_cus_send,ls_send.
    LOOP AT lt_temp INTO DATA(ls_temp) .
      READ TABLE lt_s INTO DATA(ls_s) WITH KEY companycode = ls_cus-companycode
                                               templateid = ls_temp-templateid
                                               BINARY SEARCH.
      IF sy-subrc = 0 AND ls_s-sendstatus = 'Y'."读到并且已发放
        CONTINUE.
      ELSE.
        "把尚未发放的模版ID添加进集合
        ls_temp_send-coupon_template_id = ls_temp-templateid. "优惠券模版
        IF ls_s IS NOT INITIAL .
          ls_temp_send-number  = ls_s-quantity + ( -1 ) * ls_s-couponsucess.   "发放张数 = 要发放的 - 已经发放的
        ELSE.
          ls_temp_send-number  = ls_temp-quantity.   "发放张数 = 要发放的
        ENDIF.
        APPEND ls_temp_send TO lt_temp_send.

      ENDIF.
      CLEAR:ls_temp,ls_s .
    ENDLOOP.

    "调用接口发放优惠券
    IF lt_temp_send IS INITIAL.
      CONTINUE .
    ELSE .
      SELECT SINGLE companyid INTO @DATA(lv_companyid)
       FROM ztint_cus_inf WHERE companycode = @ls_cus-companycode.

      APPEND lv_companyid TO lt_cus_send.

      "整合发送数据
      ls_send-company_ids    = lt_cus_send. "单一客户集合
      ls_send-template_items = lt_temp_send."模版集合

      "有效期
      DATA: lv_begin TYPE sy-datum,
            lv_end   TYPE sy-datum.

      lv_begin = sy-datum.
      lv_end = sy-datum + 7.
      ls_send-validity_begin_date = |{ lv_begin DATE = ISO }|.
      ls_send-validity_end_date   = |{ lv_end DATE = ISO }|.

      DATA(lo_coupon) = NEW zcl_icec_coupon_api( ).
      CALL METHOD lo_coupon->send_coupons_by_template
        EXPORTING
          is_send   = ls_send
        IMPORTING
          ev_json   = DATA(lv_json)
          et_coupon = DATA(lt_coupon)
          es_msg    = DATA(ls_msg).
      IF ls_msg-type = 'E'.
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_coupon_hq'
                                                  eventdesc = '总部营销-根据模版发放优惠券'
                                                  status = 'E'
                                                  message = '调用接口发放模版优惠券失败'
                                                  key1 = iv_spno
                                                  key2 = lv_code ).
      ELSE.

        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_coupon_hq'
                                                  eventdesc = '总部营销-根据模版发放优惠券'
                                                  status = 'S'
                                                  message = '调用发放模版优惠券成功'
                                                  key1 = iv_spno
                                                  key2 = lv_code
                                                  content = lv_json  ).  "成功的记录一下发放的优惠券id,是个集合
        "成功后返回 lt_coupon
        LOOP AT lt_temp_send INTO ls_temp_send . "
          CLEAR:ls_s .

          "对返回的本模版的优惠券id计数
          lv_cnt  = REDUCE i( INIT x = 0
                              FOR wa IN lt_coupon[]
                              WHERE ( coupontemplateid = ls_temp_send-coupon_template_id )
                              NEXT x = x + 1 ).

          READ TABLE lt_s INTO ls_s WITH KEY companycode = ls_cus-companycode
                                                   templateid = ls_temp_send-coupon_template_id
                                                   BINARY SEARCH.
          IF sy-subrc = 0."取已成功发放的数量
            ls_s-couponsucess    = ls_s-couponsucess + lv_cnt . "已发的 + 本次成功的
            IF ls_s-couponsucess = ls_s-quantity.
              ls_s-sendstatus  = 'Y'."发放成功
            ELSE.
              ls_s-sendstatus  = 'P'."部分发放
            ENDIF.
            ls_s-zupd_bdate = sy-datum.
            ls_s-zupd_btime = sy-uzeit.
            ls_s-zupd_uname = '企业微信'.

          ELSE."新增一条发放记录
            CLEAR:ls_s.
            READ TABLE lt_temp INTO ls_temp WITH KEY templateid = ls_temp_send-coupon_template_id
            BINARY SEARCH. "这里一定要取模版里面的QUANTITY
            IF sy-subrc = 0.
              MOVE-CORRESPONDING ls_temp TO ls_s .
              ls_s-companycode = ls_cus-companycode.
              ls_s-couponsucess = lv_cnt .
              IF ls_temp-quantity = lv_cnt .
                ls_s-sendstatus  = 'Y'."发放成功
              ELSE.
                ls_s-sendstatus  = 'P'."部分发放
              ENDIF.
              ls_s-senddate = ls_s-zcrt_bdate = ls_s-zupd_bdate = sy-datum.
              ls_s-sendtime = ls_s-zcrt_btime = ls_s-zupd_btime = sy-uzeit.
              ls_s-zcrt_uname = ls_s-zupd_uname = '企业微信'.
            ENDIF.
          ENDIF.

          MODIFY ztint_dp_pi_s FROM ls_s."发放的记录表【客户+每一个模版ID都记录】
          CLEAR:lv_cnt.
        ENDLOOP.
      ENDIF.
      MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.      "发放log
    ENDIF.

    CLEAR:ls_cus,ls_s,lv_json,lt_coupon,ls_msg.
  ENDLOOP.

  COMMIT WORK .

*-本流程所有客户 + 所有模版id都处理完，判断整个流程的H表状态
  DATA:lv_count TYPE i .
  SELECT SUM( couponsucess )
      FROM ztint_dp_pi_s
      INTO @DATA(lv_send)  "本流程成功发放的
      WHERE guid = @ls_ztint_req-guid.

*本流程模版需要发放的
  lv_count  = REDUCE i( INIT x = 0
                      FOR ls IN lt_temp
                      NEXT x = x + ls-quantity ).

  IF lv_send = lv_count . "只有已发的 = 要发的 ,才修改抬头表为Y
    "审批抬头表
    ls_ztint_dp_pi_h-sendstatus = 'Y'."发放成功
    ls_ztint_dp_pi_h-sendmsg = '总部营销-优惠券发放成功'.

    "营销管理主信息表
    ls_ztint_req-msg        =  '总部营销-优惠券发放成功'.
    ls_ztint_req-status     = 'APPROVE'."已审批
    ls_ztint_req-sendstatus = 'Y'."发放成功
    ls_ztint_req-senddate   = sy-datum."发放日期
    ls_ztint_req-zupd_bdate = sy-datum.
    ls_ztint_req-zupd_btime = sy-uzeit.
    ls_ztint_req-zupd_uname = '企业微信'.
    ls_ztint_req-coupouid = ''.

    IF ls_ztint_req-userid IS NOT INITIAL. "申请人userid

      "通知申请人额度生效
      CLEAR lv_date_s.
      CLEAR lv_content.
      lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
      lv_content =   '开思助手 \n '   && '您申请的【总部营销模版优惠券】已发放成功，请知悉！ \n '
      && lv_date_s.

      CLEAR ls_user_content.
      ls_user_content-content = lv_content.
      ls_user_content-userid  = ls_ztint_req-userid.
*      ls_user_content-orderno = |{ ls_ztint_req-companycode }|.
*      ls_user_content-orderdesc = |{ ls_ztint_req-companyname }|.
      ls_user_content-firstcatgory  = '客户管理'.
      ls_user_content-secondcatgory = '总部营销模版优惠券申请审批'.
      APPEND ls_user_content TO lt_user_content.
    ENDIF.

  ELSE.

    "审批抬头表
    IF lv_send NE 0.
      ls_ztint_req-sendstatus = ls_ztint_dp_pi_h-sendstatus = 'P'."部分发放
      ls_ztint_req-msg = ls_ztint_dp_pi_h-sendmsg  = '优惠券部分发放'.
    ELSE.
      ls_ztint_req-sendstatus = ls_ztint_dp_pi_h-sendstatus = 'N'."发放失败
      ls_ztint_req-msg = ls_ztint_dp_pi_h-sendmsg  = '优惠券发放失败'.
    ENDIF.

    ls_ztint_req-senddate = ls_ztint_dp_pi_h-senddate = sy-datum.
    ls_ztint_dp_pi_h-sendtime = sy-uzeit.

    "营销管理主信息表
    ls_ztint_req-status = 'APPROVE'."已审批

    ls_ztint_req-zupd_bdate = sy-datum.
    ls_ztint_req-zupd_btime = sy-uzeit.
    ls_ztint_req-zupd_uname = '企业微信'.

    "推送助手值班人员处理ztint_push_u_cf表配置
    CLEAR lv_date_s.
    CLEAR lv_content.
    lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

    lv_alert_content = '开思助手 \n '  && ls_ztint_dp_pi_h-title  && '审批后发放优惠券有异常,请关注! \n '
    && '实例号：' && ls_ztint_dp_pi_h-processinstanceid && ' \n '
    && '流程code：' && ls_ztint_dp_pi_h-processcode && ' \n '
    && lv_date_s.

    lv_fname = 'DPAPPLYSPECIALCOUPON'.
    lv_title =  '开思助手'.
    lv_msg_type = 'text'.

    CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
      EXPORTING
        iv_content = lv_alert_content
        iv_fname   = lv_fname.

  ENDIF.

  "给值到调用点
  is_dp_pi_h  = ls_ztint_dp_pi_h ."对于整个流程

  "更新表
  MODIFY ztint_market_req FROM ls_ztint_req.        "审批流程主信息表
  MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.       "钉钉审批已审批抬头
  MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i. "钉钉审批已审批详情

  "解锁表
  CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
    EXPORTING
      mode_ztint_dp_pi_h = 'E'
      mandt              = sy-mandt
      instanceid         = lv_instance.

*   推送通知
  IF lt_user_content IS NOT INITIAL.
    lv_title =  '开思助手'.
    lv_msg_type = 'text'.
    CONDENSE lv_url NO-GAPS .

    CALL FUNCTION 'Z_FMQYWX_CASSINT_PUSH_MESSAGE'
*      IN UPDATE TASK
      EXPORTING
        iv_title    = lv_title
        iv_msgtype  = lv_msg_type
        iv_url      = lv_url
      TABLES
        it_userlist = lt_user_content.
  ENDIF.

ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPLY_ICEC_CUSTOMER_BLUELIST
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD apply_icec_customer_bluelist.

    DATA:lv_title TYPE string.
    DATA:lv_log_msg TYPE string.
    DATA lv_fname TYPE string.
    DATA: lv_url TYPE string.
    DATA:lt_user_content TYPE  zsint_push_userlist_tab,
         ls_user_content TYPE  zsint_push_userlist.
    DATA: lv_creditapply   TYPE zde_intamount,
          lv_creditapply_s TYPE string.
    DATA: lv_alert_content TYPE string.
    DATA:lv_content TYPE string.
    DATA: lv_date_s TYPE string.
    DATA:lv_dats TYPE string,
         lv_tims TYPE string.
    DATA: lv_instance TYPE zde_intprocessinstanceid.
    DATA:lv_msg_type TYPE string.
    DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h,
          ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
          lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i,
          ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
          lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l,
          ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
    DATA: lv_date TYPE string,
          lv_time TYPE string.
    DATA: lv_alert_flag TYPE c.
    DATA: lv_flag TYPE c .

    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
    DATA(lv_code) = ls_detail-info-template_id.

    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
                           sendmsg = 'X' fname = 'BULELIST' ).
      RETURN.
    ENDIF.

    "审批结果
    IF iv_type = 'agree'. "审批通过
      "查找申请记录
      SELECT SINGLE * FROM ztint_bluelist INTO @DATA(ls_ztint_bluelist) WHERE instanceid = @iv_spno.
      IF sy-subrc NE 0.
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_customerbluelist'
                  eventdesc = '申请客户蓝名单' status = 'E' message = '未找到对应的申请记录'
                  key1 = iv_spno key2 = lv_code ).
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        RETURN.
      ENDIF.

      "判断是否已经申请过
      SELECT SINGLE * FROM ztint_dp_pi_h INTO CORRESPONDING FIELDS OF @ls_ztint_dp_pi_h
        WHERE processinstanceid = @iv_spno.
      IF ls_ztint_dp_pi_h-sendstatus = 'Y'. "已申请过
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_customerbluelist'
                  eventdesc = '申请客户蓝名单' status = 'E' message = '实例已申请过蓝名单'
                  key1 = iv_spno key2 = lv_code ).
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        RETURN.
      ENDIF.
      lv_instance = iv_spno.
      "锁表
      CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
        EXPORTING
          mode_ztint_dp_pi_h = 'E'
          mandt              = sy-mandt
          instanceid         = lv_instance.
      IF sy-subrc <> 0.
        "锁表失败 记录日志
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_customerbluelist'
                  eventdesc = '申请客户蓝名单' status = 'E' message = '实例申请蓝名单锁表失败'
                  key1 = iv_spno key2 = lv_code ).
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        RETURN.
      ENDIF.
      "没申请过
      "抬头信息
      ls_ztint_dp_pi_h = prepare_ztint_dp_pi_h( is_detail = ls_detail iv_type = iv_type ).
      "审批详情
      LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
        CLEAR ls_ztint_dp_pi_i.
        ls_ztint_dp_pi_i = prepare_ztint_dp_pi_i( iv_spno = iv_spno is_content = ls_content ).
        APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.
      ENDLOOP.

      "获取发起人名称
      SELECT SINGLE username INTO @DATA(lv_username) FROM ztint_user_inf
        WHERE qywxuserid = @ls_detail-info-applyer-userid.
      IF lv_username IS INITIAL.
        lv_username = '企业微信'.
      ENDIF.

      DATA:lv_body TYPE string.
      lv_body = |\{"companyId":"{ ls_ztint_bluelist-companyid }","createdBy":"{ lv_username }","type":"C",| &&
                |"assets":"{ ls_ztint_bluelist-confirmurl }","letterOfGuarantee":"{ ls_ztint_bluelist-assureurl }",| &&
                |"operateBy":"{ lv_username }"\}|.
      NEW zcl_icec_partycredit_api( )->add_credit_info( EXPORTING iv_body = lv_body
        IMPORTING ev_msg = DATA(ls_msg) es_addcredit = DATA(ls_addcredit) ).
      "调用接口失败
      IF ls_addcredit-status EQ 'true' OR ls_addcredit-status EQ 'X'.
        "申请成功
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_customerbluelist'
                  eventdesc = '申请客户蓝名单' status = 'S' message = '调用接口增加蓝名单成功'
                  key1 = iv_spno key2 = lv_code ).

        "临时额度审批抬头表
        ls_ztint_dp_pi_h-sendstatus = 'Y'."发放成功
        ls_ztint_dp_pi_h-sendmsg = '调用接口增加蓝名单成功'.

        "临时额度信息表
        ls_ztint_bluelist-msg = '申请蓝名单成功'.
        ls_ztint_bluelist-status = 'APPROVE'."已审批
        ls_ztint_bluelist-zupd_bdate = sy-datum.
        ls_ztint_bluelist-zupd_btime = sy-uzeit.
        ls_ztint_bluelist-zupd_uname = '企业微信'.

        IF ls_ztint_bluelist-originatoruserid IS NOT INITIAL.
          "通知申请人额度生效
          CLEAR lv_date_s.
          CLEAR lv_content.
          lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

          lv_content =   '开思助手 \n '   && '您申请的【蓝名单】已成功，请知悉！ \n '
           && '客户名称：' && ls_ztint_bluelist-companyname && ' \n '
           && '客户代码：' && ls_ztint_bluelist-companycode && ' \n '
           && lv_date_s.

          CLEAR ls_user_content.
          ls_user_content-content = lv_content.
          ls_user_content-userid = ls_ztint_bluelist-originatoruserid.
          ls_user_content-orderno = |{ ls_ztint_bluelist-companycode }|.
          ls_user_content-orderdesc = |{ ls_ztint_bluelist-companyname }|.
          ls_user_content-firstcatgory = '客户管理'.
          ls_user_content-secondcatgory = '蓝名单申请审批'.
          APPEND ls_user_content TO lt_user_content.
        ENDIF.
      ELSE.
        "d调用接口失败
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_customerbluelist'
                  eventdesc = '申请客户蓝名单' status = 'E' message = '调用接口增加蓝名单失败'
                  key1 = iv_spno key2 = lv_code ).

        "临时额度审批抬头表
        ls_ztint_dp_pi_h-sendstatus = 'N'."发放失败
        ls_ztint_dp_pi_h-senddate = sy-datum.
        ls_ztint_dp_pi_h-sendtime = sy-uzeit.
        ls_ztint_dp_pi_h-sendmsg = '调用接口增加蓝名单失败'.

        "临时额度信息表
        ls_ztint_bluelist-msg = '调用接口增加蓝名单失败'.
        ls_ztint_bluelist-status = 'APPROVE'."已审批
        ls_ztint_bluelist-zupd_bdate = sy-datum.
        ls_ztint_bluelist-zupd_btime = sy-uzeit.
        ls_ztint_bluelist-zupd_uname = '企业微信'.

        "推送内部人员处理
        CLEAR lv_date_s.
        CLEAR lv_content.
        lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

        lv_alert_content = '开思助手 \n '  && ls_ztint_dp_pi_h-title  && '审批后调用电商接口失败,请关注! \n '
           && '实例号：' && ls_ztint_dp_pi_h-processinstanceid && ' \n '
           && '流程code：' && ls_ztint_dp_pi_h-processcode && ' \n '
           && lv_date_s.

        lv_fname = 'DPAPPLYBLUELIST'.
        lv_title =  '开思助手'.
        lv_msg_type = 'text'.

        CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
          EXPORTING
            iv_content = lv_alert_content
            iv_fname   = lv_fname
*           IV_URL     =
          .
**        CALL FUNCTION 'Z_FMINT_ERRORALERT'
**          IN UPDATE TASK
**          EXPORTING
**            iv_title    = lv_title
**            iv_msg_type = lv_msg_type
***           IV_URL      =
**            iv_content  = lv_alert_content
**            iv_fname    = lv_fname.
      ENDIF.

      "更新表
      MODIFY ztint_bluelist FROM ls_ztint_bluelist.
      MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.
      MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i.
      "锁表
      CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
        EXPORTING
          mode_ztint_dp_pi_h = 'E'
          mandt              = sy-mandt
          instanceid         = lv_instance.

*   推送通知
      IF lt_user_content IS NOT INITIAL.
        lv_title =  '开思助手'.
        lv_msg_type = 'text'.
        CONDENSE lv_url NO-GAPS .

        CALL FUNCTION 'Z_FMQYWX_CASSINT_PUSH_MESSAGE'
          IN UPDATE TASK
          EXPORTING
            iv_title    = lv_title
            iv_msgtype = lv_msg_type
            iv_url      = lv_url
          TABLES
            it_userlist = lt_user_content.

      ENDIF.


    ELSEIF iv_type = 'refuse'."审批拒绝

      CLEAR ls_ztint_bluelist.
      SELECT SINGLE * FROM ztint_bluelist INTO CORRESPONDING FIELDS OF ls_ztint_bluelist
        WHERE instanceid = iv_spno.
      IF sy-subrc = 0.
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_customerbluelist'
                  eventdesc = '申请客户蓝名单' status = 'S' message = '更新蓝名单申请状态'
                  key1 = iv_spno key2 = lv_code ).

        ls_ztint_bluelist-status = 'UNAPPROVE'.
        ls_ztint_bluelist-msg = '审批拒绝'.
        ls_ztint_bluelist-zupd_bdate = sy-datum.
        ls_ztint_bluelist-zupd_btime = sy-uzeit.
        ls_ztint_bluelist-zupd_uname = '企业微信'.

        MODIFY ztint_bluelist FROM ls_ztint_bluelist.
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      ELSE.
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_customerbluelist'
                  eventdesc = '申请客户蓝名单' status = 'S' message = '蓝名单申请拒绝未找到对应的申请记录'
                  key1 = iv_spno key2 = lv_code ).
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      ENDIF.

    ELSEIF iv_type = 'terminate'."审批撤销
      CLEAR ls_ztint_bluelist.

      SELECT SINGLE * FROM ztint_bluelist INTO CORRESPONDING FIELDS OF ls_ztint_bluelist
        WHERE instanceid = iv_spno.

      IF sy-subrc = 0.
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_customerbluelist'
                  eventdesc = '申请客户蓝名单' status = 'S' message = '更新蓝名单申请状态撤销'
                  key1 = iv_spno key2 = lv_code ).

        ls_ztint_bluelist-status = 'CANCELED'.
        ls_ztint_bluelist-msg = '已撤销'.
        ls_ztint_bluelist-zupd_bdate = sy-datum.
        ls_ztint_bluelist-zupd_btime = sy-uzeit.
        ls_ztint_bluelist-zupd_uname = '企业微信'.

        MODIFY ztint_bluelist FROM ls_ztint_bluelist.
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      ELSE.
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_customerbluelist'
                  eventdesc = '申请客户蓝名单' status = 'S' message = '蓝名单申请撤销未找到对应的申请记录'
                  key1 = iv_spno key2 = lv_code ).
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      ENDIF.
    ENDIF.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPLY_ICEC_REGISTERCOUPON
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD apply_icec_registercoupon.

    DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h,
          ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
          lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i,
          ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
          lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l,
          ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
    DATA:lt_user_content TYPE  zsint_push_userlist_tab,
         ls_user_content TYPE  zsint_push_userlist.
    DATA:lv_body          TYPE string,
         lv_instance      TYPE zde_intprocessinstanceid,
         lv_alert_content TYPE string,
         lv_fname         TYPE string,
         lv_url           TYPE string,
         lv_title         TYPE string,
         lv_msg_type      TYPE string,
         lv_content       TYPE string,
         lv_date_s        TYPE string.

    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
    DATA(lv_code) = ls_detail-info-template_id.

    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
                           sendmsg = 'X' fname = 'REGISTERCOUPON' ).
      RETURN.
    ENDIF.


    "审批结果
    CASE iv_type.
      WHEN 'agree'. "审批通过
        "查找申请记录
        SELECT SINGLE * FROM ztint_rgcoupon INTO @DATA(ls_ztint_rgcoupon)
          WHERE instanceid = @iv_spno.
        IF sy-subrc NE 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_register_coupon'
                    eventdesc = '申请激活优惠券' status = 'E' message = '未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.
        "判断是否已经申请过
        SELECT SINGLE * FROM ztint_dp_pi_h INTO CORRESPONDING FIELDS OF @ls_ztint_dp_pi_h
          WHERE processinstanceid = @iv_spno.
        IF ls_ztint_dp_pi_h-sendstatus = 'Y'. "已申请过
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_register_coupon'
                    eventdesc = '申请激活优惠券' status = 'E' message = '实例已申请发放过激活优惠券'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.

        lv_instance = iv_spno.

        "锁表
        CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.
        IF sy-subrc <> 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_register_coupon'
                    eventdesc = '申请激活优惠券' status = 'E' message = '实例激活优惠券锁表失败'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.

        "抬头信息
        ls_ztint_dp_pi_h = prepare_ztint_dp_pi_h( is_detail = ls_detail iv_type = iv_type ).
        "审批详情
        LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
          CLEAR ls_ztint_dp_pi_i.
          ls_ztint_dp_pi_i = prepare_ztint_dp_pi_i( iv_spno = iv_spno is_content = ls_content ).
          APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.
        ENDLOOP.

        "获取最后审批人信息
*        DATA(lv_notes) = lines( ls_detail-info-sp_record )."多少审批节点
*        DATA(lv_times) = lines( ls_detail-info-sp_record[ lv_notes ]-details )."每个审批节点几个人依次审批
*        DATA(lv_finishuser) = VALUE #( ls_detail-info-sp_record[ lv_notes ]-details[ lv_times ]-approver-userid OPTIONAL ).
*        IF lv_finishuser IS NOT INITIAL.
*          SELECT SINGLE username INTO @DATA(lv_username) FROM ztint_user_inf WHERE qywxuserid = @lv_finishuser.
*        ENDIF.
*        IF lv_username IS INITIAL.
*          lv_username = '企业微信'.
*        ENDIF.
*
*        "获取发起人名称
*        SELECT SINGLE username INTO @DATA(lv_username) FROM ztint_user_inf
*          WHERE qywxuserid = @ls_detail-info-applyer-userid.
*        IF lv_username IS INITIAL.
*          lv_username = '企业微信'.
*        ENDIF.


        "调用接口发放优惠券
        DATA: lv_catagory TYPE string.
        SELECT SINGLE * FROM ztint_par INTO @DATA(ls_ztint_par)
          WHERE fname = 'REGISTERCOUPONACTIVITYID'.

        "调用发放优惠券接口
        DATA(lo_coupon) = NEW zcl_icec_coupon_api( ).
        DATA lv_companyid TYPE string.
        DATA lv_couponactivityid TYPE string.
        DATA: lv_msg TYPE bapiret2.
        lv_companyid = ls_ztint_rgcoupon-companyid.
        lv_couponactivityid  = ls_ztint_par-value.
        CALL METHOD lo_coupon->send_register_coupon
          EXPORTING
            iv_companyid        = lv_companyid
            iv_couponactivityid = lv_couponactivityid
          IMPORTING
            ev_msg              = lv_msg.

        IF lv_msg-type = 'E'.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_register_coupon'
                    eventdesc = '申请激活优惠券' status = 'E' message = '调用接口发放激活优惠券失败'
                    key1 = iv_spno key2 = lv_code ).
          "审批抬头表
          ls_ztint_dp_pi_h-sendstatus = 'N'."发放失败
          ls_ztint_dp_pi_h-senddate = sy-datum.
          ls_ztint_dp_pi_h-sendtime = sy-uzeit.
          ls_ztint_dp_pi_h-sendmsg = lv_msg-message.

          "信息表
          ls_ztint_rgcoupon-msg = lv_msg-message.
          ls_ztint_rgcoupon-status = 'APPROVE'."已审批
          ls_ztint_rgcoupon-zupd_bdate = sy-datum.
          ls_ztint_rgcoupon-zupd_btime = sy-uzeit.
          ls_ztint_rgcoupon-zupd_uname = '企业微信'.

          "推送内部人员处理
          CLEAR lv_date_s.
          CLEAR lv_content.
          lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
          lv_alert_content = '开思助手 \n '  && ls_ztint_dp_pi_h-title  && '激活优惠券审批后调用电商接口失败,请关注! \n '
             && '实例号：' && ls_ztint_dp_pi_h-processinstanceid && ' \n '
             && '流程code：' && ls_ztint_dp_pi_h-processcode && ' \n '
             && lv_date_s.

          lv_fname = 'DPAPPLYREGISTERCOUPON'.
          lv_title =  '开思助手'.
          lv_msg_type = 'text'.

          CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
            EXPORTING
              iv_content = lv_alert_content
              iv_fname   = lv_fname.

        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_register_coupon'
                    eventdesc = '申请激活优惠券' status = 'S' message = '调用发放激活优惠券成功'
                    key1 = iv_spno key2 = lv_code ).
          "审批抬头表
          ls_ztint_dp_pi_h-sendstatus = 'Y'."发放成功
          ls_ztint_dp_pi_h-sendmsg = '调用接口发放激活优惠券成功'.

          "信息表
          ls_ztint_rgcoupon-msg = '激活优惠券发放成功'.
          ls_ztint_rgcoupon-status = 'APPROVE'."已审批
          ls_ztint_rgcoupon-zupd_bdate = sy-datum.
          ls_ztint_rgcoupon-zupd_btime = sy-uzeit.
          ls_ztint_rgcoupon-zupd_uname = '企业微信'.

          IF ls_ztint_rgcoupon-originatoruserid IS NOT INITIAL.
            "通知申请人额度生效
            CLEAR lv_date_s.
            CLEAR lv_content.
            lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
            lv_content =   '开思助手 \n '   && '您申请的【激活优惠券】已发放成功，请知悉！ \n '
             && '客户名称：' && ls_ztint_rgcoupon-companyname && ' \n '
             && '客户代码：' && ls_ztint_rgcoupon-companycode && ' \n '
              && lv_date_s.

            CLEAR ls_user_content.
            ls_user_content-content = lv_content.
            ls_user_content-userid = ls_ztint_rgcoupon-originatoruserid.
            ls_user_content-orderno = |{ ls_ztint_rgcoupon-companycode }|.
            ls_user_content-orderdesc = |{ ls_ztint_rgcoupon-companyname }|.
            ls_user_content-firstcatgory = '客户管理'.
            ls_user_content-secondcatgory = '激活特殊优惠券申请审批'.
            APPEND ls_user_content TO lt_user_content.
          ENDIF.
        ENDIF.

        "更新表
        MODIFY ztint_rgcoupon FROM ls_ztint_rgcoupon.
        MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i.
        "锁表
        CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.

*   推送通知
        IF lt_user_content IS NOT INITIAL.
          lv_title =  '开思助手'.
          lv_msg_type = 'text'.
          CONDENSE lv_url NO-GAPS .

          CALL FUNCTION 'Z_FMQYWX_CASSINT_PUSH_MESSAGE'
            IN UPDATE TASK
            EXPORTING
              iv_title    = lv_title
              iv_msgtype = lv_msg_type
              iv_url      = lv_url
            TABLES
              it_userlist = lt_user_content.
        ENDIF.

      WHEN 'refuse'."审批拒绝
        CLEAR ls_ztint_rgcoupon.
        SELECT SINGLE * FROM ztint_rgcoupon INTO CORRESPONDING FIELDS OF ls_ztint_rgcoupon
          WHERE instanceid = iv_spno.
        IF sy-subrc = 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_register_coupon'
                    eventdesc = '申请激活优惠券' status = 'S' message = '更新激活优惠券申请状态'
                    key1 = iv_spno key2 = lv_code ).

          ls_ztint_rgcoupon-status = 'UNAPPROVE'.
          ls_ztint_rgcoupon-msg = '审批拒绝'.
          ls_ztint_rgcoupon-zupd_bdate = sy-datum.
          ls_ztint_rgcoupon-zupd_btime = sy-uzeit.
          ls_ztint_rgcoupon-zupd_uname = '企业微信'.

          MODIFY ztint_rgcoupon FROM ls_ztint_rgcoupon.
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_register_coupon'
                    eventdesc = '申请激活优惠券' status = 'S' message = '激活优惠券申请拒绝未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ENDIF.

      WHEN 'terminate'."审批撤销
        CLEAR ls_ztint_rgcoupon.
        SELECT SINGLE * FROM ztint_rgcoupon INTO CORRESPONDING FIELDS OF ls_ztint_rgcoupon
          WHERE instanceid = iv_spno.
        IF sy-subrc = 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_register_coupon'
                    eventdesc = '申请激活优惠券' status = 'S' message = '更新激活优惠券状态撤销'
                    key1 = iv_spno key2 = lv_code ).

          ls_ztint_rgcoupon-status = 'CANCELED'.
          ls_ztint_rgcoupon-msg = '已撤销'.
          ls_ztint_rgcoupon-zupd_bdate = sy-datum.
          ls_ztint_rgcoupon-zupd_btime = sy-uzeit.
          ls_ztint_rgcoupon-zupd_uname = '企业微信'.

          MODIFY ztint_rgcoupon FROM ls_ztint_rgcoupon.
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_register_coupon'
                    eventdesc = '申请激活优惠券' status = 'S' message = '激活优惠券申请撤销未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ENDIF.
      WHEN OTHERS.
    ENDCASE.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPLY_ICEC_SPECIALCOUPON
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
METHOD apply_icec_specialcoupon.

  DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h,
        ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
        lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i,
        ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
        lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l,
        ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
  DATA:lt_user_content TYPE  zsint_push_userlist_tab,
       ls_user_content TYPE  zsint_push_userlist.
  DATA:lv_body          TYPE string,
       lv_instance      TYPE zde_intprocessinstanceid,
       lv_alert_content TYPE string,
       lv_fname         TYPE string,
       lv_url           TYPE string,
       lv_title         TYPE string,
       lv_msg_type      TYPE string,
       lv_content       TYPE string,
       lv_date_s        TYPE string.

  DATA: lv_orderamount TYPE int4,
        lv_discount    TYPE int4.

  DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
  DATA(lv_code) = ls_detail-info-template_id.

  IF ls_detail-errcode NE '0'.
    "没找到 记录日志
    log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
    zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
    sendmsg = 'X' fname = 'SPECIALCOUPON' ).
    RETURN.
  ENDIF.


  "审批结果
  CASE iv_type.
    WHEN 'agree'. "审批通过
      "查找申请记录
      SELECT SINGLE * FROM ztint_spcoupon INTO @DATA(ls_ztint_spcoupon)
            WHERE instanceid = @iv_spno.
      IF sy-subrc NE 0.
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_special_coupon'
        eventdesc = '申请特殊优惠券' status = 'E' message = '未找到对应的申请记录'
        key1 = iv_spno key2 = lv_code ).
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        RETURN.
      ENDIF.

      "判断是否已经申请过
      SELECT SINGLE * FROM ztint_dp_pi_h INTO CORRESPONDING FIELDS OF @ls_ztint_dp_pi_h
      WHERE processinstanceid = @iv_spno.

      IF ls_ztint_dp_pi_h-sendstatus = 'Y'. "已申请过
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_special_coupon'
        eventdesc = '申请特殊优惠券' status = 'E' message = '实例已申请发放过特殊优惠券'
        key1 = iv_spno key2 = lv_code ).
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        RETURN.
      ENDIF.
      lv_instance = iv_spno.

      "锁表
      CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
        EXPORTING
          mode_ztint_dp_pi_h = 'E'
          mandt              = sy-mandt
          instanceid         = lv_instance.
      IF sy-subrc <> 0.
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_special_coupon'
        eventdesc = '申请特殊优惠券' status = 'E' message = '实例特殊优惠券锁表失败'
        key1 = iv_spno key2 = lv_code ).
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        RETURN.
      ENDIF.

      "抬头信息
      ls_ztint_dp_pi_h = prepare_ztint_dp_pi_h( is_detail = ls_detail iv_type = iv_type ).
      "审批详情
      LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
        CLEAR ls_ztint_dp_pi_i.
        ls_ztint_dp_pi_i = prepare_ztint_dp_pi_i( iv_spno = iv_spno is_content = ls_content ).
        APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.
      ENDLOOP.

      "获取发起人名称
      SELECT SINGLE username INTO @DATA(lv_username) FROM ztint_user_inf
            WHERE qywxuserid = @ls_detail-info-applyer-userid.
      IF lv_username IS INITIAL.
        lv_username = '企业微信'.
      ENDIF.

      "商品接口  获取品类
      DATA:lv_catagoryid TYPE string.
      lv_catagoryid = 'group_1'.
      DATA: lt_businesscatagoryid  TYPE zprod_t_businesscatagorys.
      DATA(lo_product) = NEW zcl_icec_product_api( ).
      DATA: lv_msg TYPE bapiret2.
      CALL METHOD lo_product->get_catagorys_firstcatagory
        EXPORTING
          iv_catagoryid         = lv_catagoryid
        IMPORTING
          ev_msg                = lv_msg
          et_businesscatagoryid = lt_businesscatagoryid.
      IF lv_msg-type = 'E'.
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_special_coupon'
        eventdesc = '申请特殊优惠券' status = 'E' message = '调用接口特殊优惠券获取品类范围失败'
        key1 = iv_spno key2 = lv_code ).

        "审批抬头表
        ls_ztint_dp_pi_h-sendstatus = 'N'."发放失败
        ls_ztint_dp_pi_h-sendmsg = '获取品类范围失败'.

        "信息表
        ls_ztint_spcoupon-msg = '获取品类范围失败'.
        ls_ztint_spcoupon-status = 'APPROVE'."已审批
        ls_ztint_spcoupon-zupd_bdate = sy-datum.
        ls_ztint_spcoupon-zupd_btime = sy-uzeit.
        ls_ztint_spcoupon-zupd_uname = '企业微信'.

        "推送内部人员处理
        CLEAR lv_date_s.
        CLEAR lv_content.
        lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
        lv_alert_content = '开思助手 \n '   && '有特殊优惠券申请审批获取品类范围失败,请关注! \n '
        && '实例号：' && iv_spno && ' \n '
        && '流程code：' && lv_code && ' \n '
        && lv_date_s.

        lv_fname = 'DPAPPLYSPECIALCOUPON'.
        lv_title =  '开思助手'.
        lv_msg_type = 'text'.

        CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
          EXPORTING
            iv_content = lv_alert_content
            iv_fname   = lv_fname.
      ELSE.  "调用接口发放优惠券
        "轮胎或者蓄电池时获取配件分类
        DATA: lv_firstlevelcategoryid  TYPE string, "一级分类
              lv_secondlevelcategoryid TYPE string, "二级分类
              lv_thirdlevelcategoryid  TYPE string.  "三级分类

        IF ls_ztint_spcoupon-categoryid = '3' OR ls_ztint_spcoupon-categoryid = '4'.
          CALL METHOD lo_product->get_catag_firstcatagory_new
            EXPORTING
              iv_categoryid = '103'
              iv_depth      = '3'
              iv_needbase   = 'Y'
            IMPORTING
              et_output     = DATA(lt_out)
              ev_msg        = DATA(ev_msg).
        ENDIF.
        IF ls_ztint_spcoupon-categoryid = '3'.
          READ TABLE lt_out INTO DATA(ls_out) WITH KEY itemname = '轮胎' level = '3'.
          IF sy-subrc = 0.
            "三级分类
            lv_thirdlevelcategoryid = ls_out-id.
            READ TABLE lt_out INTO ls_out WITH KEY id = ls_out-parentid.
            IF sy-subrc = 0.
              "二级分类
              lv_secondlevelcategoryid = ls_out-id.
              READ TABLE lt_out INTO ls_out WITH KEY id = ls_out-parentid.
              IF sy-subrc = 0.
                "一级分类
                lv_firstlevelcategoryid = ls_out-id.
              ENDIF.
            ENDIF.
          ENDIF.
        ELSEIF ls_ztint_spcoupon-categoryid = '4'.
          READ TABLE lt_out INTO ls_out WITH KEY itemname = '蓄电池' level = '3'.
          IF sy-subrc = 0.
            "三级分类
            lv_thirdlevelcategoryid = ls_out-id.
            READ TABLE lt_out INTO ls_out WITH KEY id = ls_out-parentid.
            IF sy-subrc = 0.
              "二级分类
              lv_secondlevelcategoryid = ls_out-id.
              READ TABLE lt_out INTO ls_out WITH KEY id = ls_out-parentid.
              IF sy-subrc = 0.
                "一级分类
                lv_firstlevelcategoryid = ls_out-id.
              ENDIF.
            ENDIF.
          ENDIF.
        ENDIF.

        DATA: lv_catagory TYPE string.
        lv_body = '{'.
        "品类
        lv_body = lv_body && '"category":"EC_CATEGORY"' .
        LOOP AT  lt_businesscatagoryid INTO DATA(ls_businesscatagoryid).
          IF ls_ztint_spcoupon-categoryid = '2'. "2的时候排除掉油品化学品
            SEARCH ls_businesscatagoryid-businesscatagoryname FOR '油品'.
            IF sy-subrc = 0.
              CONTINUE.
            ENDIF.
*          ELSEIF ls_ztint_spcoupon-categoryid = '3'."8881行驶系统-8900车轮及附件-8908轮胎
*            SEARCH ls_businesscatagoryid-businesscatagoryname FOR '车轮及附件'.
*            IF sy-subrc <> 0.
*              CONTINUE.
*            ENDIF.
*          ELSEIF ls_ztint_spcoupon-categoryid = '4'."7286电器系统-7734蓄电池-7742蓄电池
*            SEARCH ls_businesscatagoryid-businesscatagoryname FOR '电器系统'.
*            IF sy-subrc <> 0.
*              CONTINUE.
*            ENDIF.
          ENDIF.
          IF lv_catagory IS INITIAL .
            lv_catagory  = ls_businesscatagoryid-businesscatagoryid.
          ELSE.
            lv_catagory = lv_catagory && ',' && ls_businesscatagoryid-businesscatagoryid.
          ENDIF.
        ENDLOOP.
        lv_catagory = lv_catagory && ',' &&  'OTHER_CATEGORY'.
        "轮胎蓄电池不传平台会根据couponProductCategory算出categoryCondition
        IF ls_ztint_spcoupon-categoryid = '3' OR ls_ztint_spcoupon-categoryid = '4'.
          CLEAR lv_catagory.
        ENDIF.

        lv_body = lv_body && ',"categoryCondition":"' && lv_catagory &&  '"' .
        lv_body = lv_body && ',"productCategory":"' && lv_catagory &&  '"' .
        "公司ID
        lv_body = lv_body && ',"companyId":"' && ls_ztint_spcoupon-companyid && '"'.
        "费用归属
        lv_body = lv_body && ',"costAscriptionType":"PERSON"'.
        "优惠券面值
        lv_body = lv_body && ',"couponAmount":"' && ls_ztint_spcoupon-discount && '"'.
        "商品品类（新业务分类）
        IF ls_ztint_spcoupon-categoryid = '3' OR ls_ztint_spcoupon-categoryid = '4'."轮胎或蓄电池时传配件分类couponProductCategory
          lv_body = |{ lv_body },"couponProductCategory":| && '{' &&
          |"firstLevelCategoryId": "{ lv_firstlevelcategoryid }", "secondLevelCategoryId": "{ lv_secondlevelcategoryid }", "thirdLevelCategoryId": "{ lv_thirdlevelcategoryid }"| && '}'.
        ENDIF.
        "优惠券类型
        lv_body = lv_body && ',"couponType":"REACH_AMOUNT_REDUCE"'.
        "订单金额
        lv_body = lv_body && ',"requirementAmount":"' && ls_ztint_spcoupon-amount && '"'.
        "优惠券名称
        lv_orderamount = ls_ztint_spcoupon-amount.
        lv_discount = ls_ztint_spcoupon-discount.
        lv_body = lv_body && ',"name":"满' && lv_orderamount  && '减' && lv_discount && '"'.
        "订单类型
        "1商城订单 2询价订单
        IF ls_ztint_spcoupon-ordertypeid = '2'.
          lv_body = lv_body && ',"orderType":"CUSTOMIZE_INQUIRY,COMMON_INQUIRY,DISTRIBUTION_INQUIRY"'.
        ELSEIF   ls_ztint_spcoupon-ordertypeid = '1'.
          lv_body = lv_body && ',"orderType":"MALL"'.
        ENDIF.
        lv_body = lv_body && ',"createdBy":"' && lv_username && '"'.
        "支付方式
        lv_body = lv_body && ',"payment":"CASH,LOAN,CREDIT"'.
        "可以渠道
        lv_body = lv_body && ',"availableChannel":"PC,APP"'.
        "可以店铺
        lv_body = lv_body && ',"storeCodes":"ALL"'.
        "有效期
        DATA: lv_begin TYPE sy-datum,
              lv_end   TYPE sy-datum.
        lv_begin = sy-datum.
        lv_end = sy-datum + 7.
        lv_body = lv_body && ',"validityBeginDate":"' && lv_begin+0(4) && '-' && lv_begin+4(2) && '-' && lv_begin+6(2) && '"'.
        lv_body = lv_body && ',"validityEndDate":"' && lv_end+0(4) && '-' && lv_end+4(2) && '-'  && lv_end+6(2) && '"'.
        lv_body = lv_body && '}'.
        "调用发放优惠券接口
        DATA: lv_id TYPE string.
        DATA(lo_coupon) = NEW zcl_icec_coupon_api( ).
        CALL METHOD lo_coupon->send_special_coupon
          EXPORTING
            iv_data = lv_body
          IMPORTING
            ev_msg  = lv_msg
            ev_id   = lv_id.
        IF lv_msg-type = 'E'.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_special_coupon'
          eventdesc = '申请特殊优惠券' status = 'E' message = '调用接口发放特殊优惠券失败'
          key1 = iv_spno key2 = lv_code ).

          "临时额度审批抬头表
          ls_ztint_dp_pi_h-sendstatus = 'N'."发放失败
          ls_ztint_dp_pi_h-senddate = sy-datum.
          ls_ztint_dp_pi_h-sendtime = sy-uzeit.
          ls_ztint_dp_pi_h-sendmsg = lv_msg-message.

          "临时额度信息表
          ls_ztint_spcoupon-msg = lv_msg-message.
          ls_ztint_spcoupon-status = 'APPROVE'."已审批
          ls_ztint_spcoupon-zupd_bdate = sy-datum.
          ls_ztint_spcoupon-zupd_btime = sy-uzeit.
          ls_ztint_spcoupon-zupd_uname = '钉钉'.

          "推送内部人员处理
          CLEAR lv_date_s.
          CLEAR lv_content.
          lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

          lv_alert_content = '开思助手 \n '  && ls_ztint_dp_pi_h-title  && '审批后调用电商接口失败,请关注! \n '
          && '实例号：' && ls_ztint_dp_pi_h-processinstanceid && ' \n '
          && '流程code：' && ls_ztint_dp_pi_h-processcode && ' \n '
          && lv_date_s.

          lv_fname = 'DPAPPLYSPECIALCOUPON'.
          lv_title =  '开思助手'.
          lv_msg_type = 'text'.

          CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
            EXPORTING
              iv_content = lv_alert_content
              iv_fname   = lv_fname.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_special_coupon'
          eventdesc = '申请特殊优惠券' status = 'S' message = '调用发放特殊优惠券成功'
          key1 = iv_spno key2 = lv_code ).

          "临时额度审批抬头表
          ls_ztint_dp_pi_h-sendstatus = 'Y'."发放成功
          ls_ztint_dp_pi_h-sendmsg = '调用接口增加蓝名单成功'.

          "临时额度信息表
          ls_ztint_spcoupon-msg = '特殊优惠券发放成功'.
          ls_ztint_spcoupon-status = 'APPROVE'."已审批
          ls_ztint_spcoupon-zupd_bdate = sy-datum.
          ls_ztint_spcoupon-zupd_btime = sy-uzeit.
          ls_ztint_spcoupon-zupd_uname = '企业微信'.
          ls_ztint_spcoupon-coupouid = lv_id.
          IF ls_ztint_spcoupon-originatoruserid IS NOT INITIAL.
            "通知申请人额度生效
            CLEAR lv_date_s.
            CLEAR lv_content.
            lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
            lv_content =   '开思助手 \n '   && '您申请的【特殊优惠券】已发放成功，请知悉！ \n '
            && '客户名称：' && ls_ztint_spcoupon-companyname && ' \n '
            && '客户代码：' && ls_ztint_spcoupon-companycode && ' \n '
            && '优惠券：' && '满' && ls_ztint_spcoupon-amount && '减' && ls_ztint_spcoupon-discount && ' \n '
            && lv_date_s.

            CLEAR ls_user_content.
            ls_user_content-content = lv_content.
            ls_user_content-userid = ls_ztint_spcoupon-originatoruserid.
            ls_user_content-orderno = |{ ls_ztint_spcoupon-companycode }|.
            ls_user_content-orderdesc = |{ ls_ztint_spcoupon-companyname }|.
            ls_user_content-firstcatgory = '客户管理'.
            ls_user_content-secondcatgory = '特殊优惠券申请审批'.
            APPEND ls_user_content TO lt_user_content.
          ENDIF.
        ENDIF.
      ENDIF.

      "更新表
      MODIFY ztint_spcoupon FROM ls_ztint_spcoupon.
      MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.
      MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i.
      "锁表
      CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
        EXPORTING
          mode_ztint_dp_pi_h = 'E'
          mandt              = sy-mandt
          instanceid         = lv_instance.

*   推送通知
      IF lt_user_content IS NOT INITIAL.
        lv_title =  '开思助手'.
        lv_msg_type = 'text'.
        CONDENSE lv_url NO-GAPS .

        CALL FUNCTION 'Z_FMQYWX_CASSINT_PUSH_MESSAGE'
          IN UPDATE TASK
          EXPORTING
            iv_title    = lv_title
            iv_msgtype  = lv_msg_type
            iv_url      = lv_url
          TABLES
            it_userlist = lt_user_content.
      ENDIF.

    WHEN 'refuse'."审批拒绝
      CLEAR ls_ztint_spcoupon.
      SELECT SINGLE * FROM ztint_spcoupon INTO CORRESPONDING FIELDS OF ls_ztint_spcoupon
      WHERE instanceid = iv_spno.
      IF sy-subrc = 0.
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_special_coupon'
        eventdesc = '申请特殊优惠券' status = 'S' message = '更新特殊优惠券申请状态'
        key1 = iv_spno key2 = lv_code ).

        ls_ztint_spcoupon-status = 'UNAPPROVE'.
        ls_ztint_spcoupon-msg = '审批拒绝'.
        ls_ztint_spcoupon-zupd_bdate = sy-datum.
        ls_ztint_spcoupon-zupd_btime = sy-uzeit.
        ls_ztint_spcoupon-zupd_uname = '企业微信'.

        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        MODIFY ztint_spcoupon FROM ls_ztint_spcoupon.
      ELSE.

        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_special_coupon'
        eventdesc = '申请特殊优惠券' status = 'E' message = '特殊优惠券申请拒绝未找到对应的申请记录'
        key1 = iv_spno key2 = lv_code ).
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      ENDIF.

    WHEN 'terminate'."审批撤销 CLEAR ls_ztint_spcoupon.
      SELECT SINGLE * FROM ztint_spcoupon INTO CORRESPONDING FIELDS OF ls_ztint_spcoupon
      WHERE instanceid = iv_spno.
      IF sy-subrc = 0.
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_special_coupon'
        eventdesc = '申请特殊优惠券' status = 'S' message = '更新特殊优惠券状态撤销'
        key1 = iv_spno key2 = lv_code ).

        ls_ztint_spcoupon-status = 'CANCELED'.
        ls_ztint_spcoupon-msg = '已撤销'.
        ls_ztint_spcoupon-zupd_bdate = sy-datum.
        ls_ztint_spcoupon-zupd_btime = sy-uzeit.
        ls_ztint_spcoupon-zupd_uname = '企业微信'.

        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        MODIFY ztint_spcoupon FROM ls_ztint_spcoupon.
      ELSE.
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_special_coupon'
        eventdesc = '申请特殊优惠券' status = 'E' message = '特殊优惠券申请撤销未找到对应的申请记录'
        key1 = iv_spno key2 = lv_code ).
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      ENDIF.
    WHEN OTHERS.
  ENDCASE.
ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPLY_ICEC_SPECIALCOUPON_NEW
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* | [--->] IV_CODE                        TYPE        STRING(optional)
* | [--->] IV_NUMBER                      TYPE        INT4(optional)
* | [--->] IS_DETAIL                      TYPE        TS_APPROVALDETAIL(optional)
* | [--->] IV_USERLOGINID                 TYPE        STRING(optional)
* | [<---] IS_DP_PI_H                     TYPE        ZTINT_DP_PI_H
* | [<---] IV_SUCCESSNUM                  TYPE        INT4
* +--------------------------------------------------------------------------------------</SIGNATURE>
METHOD apply_icec_specialcoupon_new.
*连队&&总部优惠券的发放，统一写表ztint_dp_pi_s

  DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h, "钉钉审批已审批抬头
        ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
        lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i, "钉钉审批已审批详情
        ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
        lt_ztint_dp_pi_s TYPE STANDARD TABLE OF ztint_dp_pi_s, "审批发放明细详情（优惠券、金币、积分）
        ls_ztint_dp_pi_s LIKE LINE OF lt_ztint_dp_pi_s,

        lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l, "钉钉审批已审批log
        ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
  DATA:lt_user_content TYPE  zsint_push_userlist_tab,
       ls_user_content TYPE  zsint_push_userlist.
  DATA:lv_body          TYPE string,
       lv_instance      TYPE zde_intprocessinstanceid,
       lv_alert_content TYPE string,
       lv_fname         TYPE string,
       lv_url           TYPE string,
       lv_title         TYPE string,
       lv_msg_type      TYPE string,
       lv_content       TYPE string,
       lv_date_s        TYPE string.

  DATA: lv_orderamount TYPE int4,
        lv_discount    TYPE int4.
  DATA:ls_send TYPE zsint_ml_coupon . "开思券的发放

  DATA:lv_desc TYPE string.
  DATA:lv_code TYPE string.
  TYPES:ty_p TYPE p LENGTH 5  DECIMALS 1. "9.8
  DATA lv_successnum TYPE i. "成功发放的优惠券张数
*审批结果 agree 发放

  IF is_detail IS INITIAL . "开始不发放，后来发放时单独调用一次【二期批量发放使用】
    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).  "审批流的实例单号202503190002
    lv_code   = ls_detail-info-template_id. "模版code
    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail_new'
                           zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败'
                           zvalue1 = iv_spno
                           zvalue2 = lv_code
                           sendmsg = 'X'
                           fname = 'BULELIST' ).
      RETURN.
    ENDIF.
  ELSE.
    ls_detail = is_detail .
    lv_code = iv_code .
  ENDIF.

  "获取申请记录
  SELECT SINGLE * FROM ztint_market_req
    INTO @DATA(ls_market_req)
    WHERE instanceid = @iv_spno.
  IF sy-subrc NE 0.
    "没找到 记录日志
    ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_special_coupon_new'
                                              eventdesc = '总部营销-申请特殊优惠券'
                                              status = 'E'
                                              message = '未找到对应的申请记录'
                                              key1 = iv_spno
                                              key2 = lv_code ).
    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
    RETURN.
  ENDIF.

*判断是否已经全部发放过
  SELECT SINGLE *
    FROM ztint_dp_pi_h
    INTO CORRESPONDING FIELDS OF @ls_ztint_dp_pi_h
    WHERE processinstanceid = @iv_spno.

  IF ls_ztint_dp_pi_h-sendstatus = 'Y' "已发放过（备案审批流直接发放的）
     OR ls_market_req-sendstatus = 'Y' . "因为先备案，后发放审批流发放的，原来的备案审批流是没有发放记录的！！！
    ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_coin_hq'
                                              eventdesc = '总部营销-申请特殊优惠券'
                                              status = 'E'
                                              message = '实例已申请发放过优惠券'
                                              key1 = iv_spno
                                              key2 = lv_code ).

    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
    RETURN.
  ENDIF.

  lv_instance = iv_spno.

  "锁表
  CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
    EXPORTING
      mode_ztint_dp_pi_h = 'E'
      mandt              = sy-mandt
      instanceid         = lv_instance.

  IF sy-subrc <> 0.
    ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_special_coupon_new'
                                              eventdesc = '营销管理-申请特殊优惠券'
                                              status = 'E'
                                              message = '实例特殊优惠券锁表失败'
                                              key1 = iv_spno
                                              key2 = lv_code ).

    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
    RETURN.
  ENDIF.

  "抬头信息——这里第一次写该表
  ls_ztint_dp_pi_h = prepare_ztint_dp_pi_h( is_detail = ls_detail iv_type = iv_type ).

  "审批详情
  LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
    CLEAR ls_ztint_dp_pi_i.
    ls_ztint_dp_pi_i = prepare_ztint_dp_pi_i( iv_spno = iv_spno is_content = ls_content ).
    APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.  "这里只是存储，没有像发金币一样动态模版数据
  ENDLOOP.

*-已发放的清单
*  disinstanceid  发放审批流 和 自身guid对应
*  instanceid     原报备审批流
  SELECT *   "唯一一条
    FROM ztint_dp_pi_s
    INTO TABLE @DATA(lt_s)
    WHERE guid = @ls_market_req-guid."含申请流发放 && 备案后的发放审批流
  SORT lt_s BY disinstanceid .

  "获取发起人名称
  SELECT SINGLE username
    INTO @DATA(lv_username)
    FROM ztint_user_inf
    WHERE qywxuserid = @ls_detail-info-applyer-userid.
  IF lv_username IS INITIAL.
    lv_username = '企业微信'.
  ENDIF.

  "商品接口  获取品类
  DATA:lv_catagoryid TYPE string.
  lv_catagoryid = 'group_1'.
  DATA:lt_businesscatagoryid  TYPE zprod_t_businesscatagorys.

  DATA(lo_product) = NEW zcl_icec_product_api( ).
  DATA: lv_msg TYPE bapiret2.
  CALL METHOD lo_product->get_catagorys_firstcatagory
    EXPORTING
      iv_catagoryid         = lv_catagoryid
    IMPORTING
      ev_msg                = lv_msg
      et_businesscatagoryid = lt_businesscatagoryid.  "商品品类集合

  IF lv_msg-type = 'E'.
    ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_special_coupon_new'
                                              eventdesc = '营销管理-申请特殊优惠券'
                                              status = 'E'
                                              message = '调用接口特殊优惠券获取品类范围失败'
                                              key1 = iv_spno
                                              key2 = lv_code ).

    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.      "钉钉审批已审批log

    "审批抬头表
    ls_ztint_dp_pi_h-sendstatus = 'N'."发放失败
    ls_ztint_dp_pi_h-sendmsg = '获取品类范围失败'.

    "信息表
    ls_market_req-msg = '获取品类范围失败'.
    ls_market_req-status = 'APPROVE'." 审批状态 - 已审批
    ls_market_req-zupd_bdate = sy-datum.
    ls_market_req-zupd_btime = sy-uzeit.
    ls_market_req-zupd_uname = '企业微信'.

    "推送内部人员处理
    CLEAR lv_date_s.
    CLEAR lv_content.
    lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
    lv_alert_content = '开思助手 \n '   && '营销管理-有特殊优惠券申请审批获取品类范围失败,请关注! \n '
    && '实例号：' && iv_spno && ' \n '
    && '流程code：' && lv_code && ' \n '
    && lv_date_s.

    lv_fname = 'DPAPPLYSPECIALCOUPON'.
    lv_title =  '开思助手'.
    lv_msg_type = 'text'.

    CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
      EXPORTING
        iv_content = lv_alert_content
        iv_fname   = lv_fname.

  ELSE.  "调用接口发放优惠券

    DATA:lv_catagory TYPE string.

    "适用品类
    ls_send-category = 'EC_CATEGORY' .

    "品类
    LOOP AT lt_businesscatagoryid INTO DATA(ls_businesscatagoryid). "商品品类集合

      IF ls_market_req-categoryid = 'B'. "B的时候仅限油品
        SEARCH ls_businesscatagoryid-businesscatagoryname FOR '油品'.
        IF sy-subrc NE 0.
          CONTINUE.
        ENDIF.
      ENDIF.

      IF ls_market_req-categoryid = 'C'. "C的时候排除掉油品、化学品
        SEARCH ls_businesscatagoryid-businesscatagoryname FOR '油品'.
        IF sy-subrc = 0.
          CONTINUE.
        ENDIF.
      ENDIF.

      IF lv_catagory IS INITIAL .
        lv_catagory  = ls_businesscatagoryid-businesscatagoryid.
      ELSE.
        lv_catagory = lv_catagory && ',' && ls_businesscatagoryid-businesscatagoryid.
      ENDIF.

    ENDLOOP.

    lv_catagory = lv_catagory && ',' &&  'OTHER_CATEGORY'.

    ls_send-category_condition = lv_catagory.
    ls_send-product_category   = lv_catagory.

    "限定品牌brand
    IF ls_market_req-brandid IS NOT INITIAL . "新品导入 ，品牌新客 -限制品牌
      ls_send-product_brand = ls_market_req-brandid .
    ENDIF.

    "公司ID
    ls_send-company_id = ls_market_req-companyid .
    ls_send-company_id = ls_market_req-companyid .
    ls_send-company_id = ls_market_req-companyid .

    "费用归属
    ls_send-cost_ascription_type = 'PERSON'.

    "优惠券类型
    IF ls_market_req-coupontype = 'A01' ."满减券

      "优惠券面值  四舍五入
      ls_send-coupon_amount = round( val = ls_market_req-offermoney dec = 0 )."满减券面值
      ls_send-coupon_type = 'REACH_AMOUNT_REDUCE'."满减券

      "优惠券名称
      lv_orderamount = ls_market_req-orderamountmin.    "订单要求金额1000
      lv_discount    = ls_market_req-offermoney.       "满1000减50金额
      lv_desc = |满{ lv_orderamount }减{ lv_discount }|.

    ELSEIF ls_market_req-coupontype = 'A02' ."折扣券

      "优惠券面值
      ls_send-coupon_amount = CONV ty_p( ls_market_req-discountratio ).""折扣券的折扣值9.8
      ls_send-coupon_type = 'DISCOUNT'."折扣券

      lv_orderamount = ls_market_req-orderamountmin.    "订单要求金额1000
      "优惠券名称：“满【订单金额要求】- {【折扣值】*10 } 折（最高【折扣券上限】）

      DATA(lv_p) = ls_market_req-discountratio  * 10 . " 9.8 * 10
      lv_desc = |满{ lv_orderamount }-{ lv_p }折(最高{ ls_market_req-discountmax })|."折扣券名称

      ""折扣上限金额
      ls_send-discount_ceiling_amount = ls_market_req-discountmax.
    ENDIF.

    "优惠券名称
    ls_send-name = lv_desc .

    "最低要求的订单金额
    ls_send-requirement_amount = ls_market_req-orderamountmin .

    "订单类型
    "1商城订单 2询价订单
    IF ls_market_req-ordertype = '2'.
      ls_send-order_type = 'CUSTOMIZE_INQUIRY,COMMON_INQUIRY,DISTRIBUTION_INQUIRY'.
    ELSEIF ls_market_req-ordertype = '1'.
      ls_send-order_type = 'MALL'.
    ENDIF.

    "发放人
    ls_send-created_by = lv_username.

    "支付方式
    ls_send-payment = 'CASH,LOAN,CREDIT'.

    "可用渠道
    ls_send-available_channel = 'PC,APP'.

    "限定店铺 - 申请类型为新品导入时，限定店铺，其他为ALL
    DATA:lt_storeid TYPE TABLE OF ztint_ml_conf,
         lv_where   TYPE string.
    IF ls_market_req-reqtype = 'C' ."新品导入时

      IF ls_market_req-mltype = 'C4'. "品牌新客-取配置表
        SELECT storeid
          INTO CORRESPONDING FIELDS OF TABLE @lt_storeid
          FROM ztint_ml_conf
          WHERE brandid = @ls_market_req-brandid.
      ELSE. "C1 严选新客 C2共享仓新客 C3易损件新客

        lv_where = SWITCH #( ls_market_req-mltype
                              WHEN  'C1'  THEN |ZISMONO IN ('A','D')|
                              WHEN  'C2'  THEN |ISYUN EQ '1'|
                              WHEN  'C3'  THEN |ISFIRSTVULN EQ '1'|
                              ).

        SELECT productstoreid AS storeid
           INTO CORRESPONDING FIELDS OF TABLE @lt_storeid
           FROM ztint_venstore_f
           WHERE yearmonth = @sy-datum(6)  "取发放时点的店铺
           AND (lv_where).

      ENDIF.

      "取限制店铺的集合 X1,X2,X3
      SORT lt_storeid BY storeid .
      DELETE ADJACENT DUPLICATES FROM lt_storeid COMPARING storeid .

      DATA(lv_store) = REDUCE string(
                 INIT x = ||
                 FOR wa IN lt_storeid
                 NEXT x = COND #( WHEN x = '' THEN |{ wa-storeid }|
                                  ELSE |{ x },{ wa-storeid }| )
                                     ) .
      ls_send-store_codes = lv_store.
    ELSE.
      "其余的全店铺
      ls_send-store_codes = 'ALL'.
    ENDIF.

    "备注 remark
    SELECT SINGLE  reqname INTO @ls_send-remark
      FROM ztint_reqtype
      WHERE reqtype = @ls_market_req-reqtype .

    ls_send-attach_type = 'ALL'. "平台商家

    "有效期
    DATA: lv_begin TYPE sy-datum,
          lv_end   TYPE sy-datum.

    lv_begin = sy-datum.
    lv_end = sy-datum + 7.
    ls_send-validity_begin_date = |{ lv_begin DATE = ISO }|.
    ls_send-validity_end_date   = |{ lv_end DATE = ISO }|.

    "获取要发送的json串
    CALL FUNCTION 'Z_FMBC_GET_JSON'
      EXPORTING
        iv_data = ls_send
      IMPORTING
        ev_json = lv_body.

*-考虑多张优惠券的发放逻辑
*只有全部成功时，才算成功，有一张不成功即为失败

    "调用发放优惠券接口
    DATA:lv_id TYPE string.
    DATA:lv_cnt   TYPE i , "需要发放的次数
         lv_total TYPE i . "总计发放成功的次数
    lv_cnt = ls_market_req-couponnum + ( -1 ) * ls_market_req-couponsucess . " 还需要发送的 = 需要发放的 - 已成功发放的

    IF iv_number IS NOT INITIAL."手动发放
      lv_cnt = iv_number.
    ENDIF.

    CLEAR lv_successnum.
    DO lv_cnt TIMES. "因为平台的特殊优惠券接口每次只能发放一张
      DATA(lo_coupon) = NEW zcl_icec_coupon_api( ).
      CALL METHOD lo_coupon->send_special_coupon
        EXPORTING
          iv_data = lv_body
        IMPORTING
          ev_msg  = lv_msg
          ev_id   = lv_id. "发放成功时-优惠券id
      IF lv_msg-type = 'E'.
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_special_coupon_new'
                                                  eventdesc = '营销管理-申请特殊优惠券'
                                                  status = 'E'
                                                  message = '调用接口发放特殊优惠券失败'
                                                  key1 = iv_spno
                                                  key2 = lv_code ).
      ELSE.
        "成功后计数 + 1
        ls_market_req-couponsucess = ls_market_req-couponsucess + 1 .

        lv_successnum = lv_successnum + 1.

        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_special_coupon_new'
                                                  eventdesc = '营销管理-申请特殊优惠券'
                                                  status = 'S'
                                                  message = '调用发放特殊优惠券成功'
                                                  key1 = iv_spno
                                                  key2 = lv_code
                                                  key3 = lv_id  ).  "成功的记录一下发放的优惠券id
      ENDIF.
      MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.      "钉钉审批已审批log
    ENDDO.

    IF lv_successnum NE 0."本次发放成功的不为0

      iv_successnum = lv_successnum."接口出参返回具体发放成功数量

      READ TABLE lt_s INTO DATA(ls_s) WITH KEY disinstanceid = ls_market_req-instanceid
                                           BINARY SEARCH.

      IF sy-subrc = 0."读到更新【这个逻辑处理PC端list的多次发放】

        ls_s-couponsucess   = ls_s-couponsucess + lv_successnum. "已发的 + 本次成功的
        IF ls_s-quantity = ls_s-couponsucess ."
          ls_s-sendstatus  = 'Y'."发放成功
        ELSE.
          ls_s-sendstatus  = 'P'."部分发放
        ENDIF.
        ls_s-zupd_bdate = sy-datum.
        ls_s-zupd_btime = sy-uzeit.
        ls_s-zupd_uname = '企业微信'.

      ELSE."没读到新增
        CLEAR:ls_s.
        MOVE-CORRESPONDING ls_market_req TO ls_s . "成功发放的张数，在这里
        ls_s = CORRESPONDING #( ls_market_req  MAPPING vtext = companyname
                                                       disinstanceid = instanceid  "发放审理流(1连队的发放是其本身)
                                                       instanceid    = sourceinstanceid  "原报备审批流
                                                       quantity      = couponnum  "需发放的张数

        ).

        IF ls_s-quantity = lv_successnum. "本次发的 = 要发的
          ls_s-sendstatus  = 'Y'."发放成功
        ELSE.
          ls_s-sendstatus  = 'P'."部分发放
        ENDIF.

        ls_s-senddate = ls_s-zcrt_bdate = ls_s-zupd_bdate = sy-datum.
        ls_s-sendtime = ls_s-zcrt_btime = ls_s-zupd_btime = sy-uzeit.
        ls_s-zcrt_uname = ls_s-zupd_uname = '企业微信'.

      ENDIF.
    ENDIF.

    IF ls_market_req-couponnum NE ls_market_req-couponsucess . "需要发送的张数 不等于 已成功发送的张数 ，定义为失败
      "审批抬头表
      IF ls_market_req-couponsucess NE 0.
        ls_ztint_dp_pi_h-sendstatus = 'P'."部分发放
      ELSE.
        ls_ztint_dp_pi_h-sendstatus = 'N'."发放失败
      ENDIF.

      ls_ztint_dp_pi_h-senddate = sy-datum.
      ls_ztint_dp_pi_h-sendtime = sy-uzeit.
      ls_ztint_dp_pi_h-sendmsg  = lv_msg-message.

      "营销管理主信息表
      ls_market_req-sendmsg  = lv_msg-message.
      ls_market_req-status = 'APPROVE'."已审批
      IF ls_market_req-couponsucess NE 0.
        ls_market_req-sendstatus = 'P'."部分发放
        ls_market_req-senddate = sy-datum."发放日期
      ELSE.
        ls_market_req-sendstatus = 'N'."发放失败
      ENDIF.
      ls_market_req-zupd_bdate = sy-datum.
      ls_market_req-zupd_btime = sy-uzeit.
      ls_ztint_dp_pi_h-zcrt_uname = ls_ztint_dp_pi_h-zupd_uname = ls_market_req-zupd_uname = '企业微信'.

      "推送助手值班人员处理
      CLEAR lv_date_s.
      CLEAR lv_content.
      lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

      lv_alert_content = '开思助手 \n '  && ls_ztint_dp_pi_h-title  && '审批后调用电商接口失败,请关注! \n '
      && '实例号：' && ls_ztint_dp_pi_h-processinstanceid && ' \n '
      && '流程code：' && ls_ztint_dp_pi_h-processcode && ' \n '
      && lv_date_s.

      lv_fname = 'DPAPPLYSPECIALCOUPON'.
      lv_title =  '开思助手'.
      lv_msg_type = 'text'.

      CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
        EXPORTING
          iv_content = lv_alert_content
          iv_fname   = lv_fname.

    ELSE .
      "审批抬头表
      ls_ztint_dp_pi_h-sendstatus = 'Y'."发放成功
      ls_ztint_dp_pi_h-sendmsg = '特殊优惠券发放成功'.

      "营销管理主信息表
      ls_market_req-msg = '特殊优惠券发放成功'.
      ls_market_req-status = 'APPROVE'."已审批
      ls_market_req-sendstatus = 'Y'."发放成功
      ls_market_req-senddate = sy-datum."发放日期
      ls_market_req-zupd_bdate = sy-datum.
      ls_market_req-zupd_btime = sy-uzeit.
      ls_ztint_dp_pi_h-zcrt_uname = ls_ztint_dp_pi_h-zupd_uname = ls_market_req-zupd_uname = '企业微信'.
      ls_market_req-coupouid = lv_id.  "记录一个即可

      IF ls_market_req-userid IS NOT INITIAL. "申请人userid

        "通知申请人额度生效
        CLEAR lv_date_s.
        CLEAR lv_content.
        lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
        lv_content =   '开思助手 \n '   && '您申请的【特殊优惠券】已发放成功，请知悉！ \n '
        && '客户名称：' && ls_market_req-companyname && ' \n '
        && '客户代码：' && ls_market_req-companycode && ' \n '
        && '优惠券：' && lv_desc && ' \n '  "满1000减50
        && lv_date_s.

        CLEAR ls_user_content.
        ls_user_content-content = lv_content.
        ls_user_content-userid = ls_market_req-userid.
        ls_user_content-orderno = |{ ls_market_req-companycode }|.
        ls_user_content-orderdesc = |{ ls_market_req-companyname }|.
        ls_user_content-firstcatgory  = '客户管理'.
        ls_user_content-secondcatgory = '特殊优惠券申请审批'.
        APPEND ls_user_content TO lt_user_content.
      ENDIF.
    ENDIF.
  ENDIF .

  "给值到调用点
  is_dp_pi_h  = ls_ztint_dp_pi_h .
  "更新表
  MODIFY ztint_market_req FROM ls_market_req.       "审批流程主信息表
  MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.      "钉钉审批已审批抬头
  MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i."钉钉审批已审批详情
  MODIFY ztint_dp_pi_s FROM ls_s."发放的记录表

  "解锁表
  CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
    EXPORTING
      mode_ztint_dp_pi_h = 'E'
      mandt              = sy-mandt
      instanceid         = lv_instance.

*   推送通知
  IF lt_user_content IS NOT INITIAL.
    lv_title =  '开思助手'.
    lv_msg_type = 'text'.
    CONDENSE lv_url NO-GAPS .

    CALL FUNCTION 'Z_FMQYWX_CASSINT_PUSH_MESSAGE'
*      IN UPDATE TASK
      EXPORTING
        iv_title    = lv_title
        iv_msgtype  = lv_msg_type
        iv_url      = lv_url
      TABLES
        it_userlist = lt_user_content.
  ENDIF.

ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPLY_ICEC_SPECIALCOUPON_V2
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* | [--->] IV_CODE                        TYPE        STRING(optional)
* | [--->] IV_NUMBER                      TYPE        INT4(optional)
* | [--->] IS_DETAIL                      TYPE        TS_APPROVALDETAIL(optional)
* | [--->] IV_USERLOGINID                 TYPE        STRING(optional)
* | [<---] IS_DP_PI_H                     TYPE        ZTINT_DP_PI_H
* | [<---] IV_SUCCESSNUM                  TYPE        INT4
* +--------------------------------------------------------------------------------------</SIGNATURE>
METHOD apply_icec_specialcoupon_v2.

  DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h, "钉钉审批已审批抬头
        ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
        lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i, "钉钉审批已审批详情
        ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
        lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l, "钉钉审批已审批log
        ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
  DATA:lt_user_content TYPE  zsint_push_userlist_tab,
       ls_user_content TYPE  zsint_push_userlist.
  DATA:lv_body          TYPE string,
       lv_instance      TYPE zde_intprocessinstanceid,
       lv_alert_content TYPE string,
       lv_fname         TYPE string,
       lv_url           TYPE string,
       lv_title         TYPE string,
       lv_msg_type      TYPE string,
       lv_content       TYPE string,
       lv_date_s        TYPE string.

  DATA: lv_orderamount TYPE int4,
        lv_discount    TYPE int4.
  DATA:ls_send TYPE zsint_ml_coupon . "开思券的发放

  DATA:lv_desc TYPE string.
  DATA:lv_code TYPE string.
  TYPES:ty_p TYPE p LENGTH 5  DECIMALS 1. "9.8
  DATA lv_successnum TYPE i. "成功发放的优惠券张数
*审批结果 agree 发放

  IF is_detail IS INITIAL . "开始不发放，后来发放时单独调用一次【二期批量发放使用】
    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).  "审批流的实例单号202503190002
    lv_code   = ls_detail-info-template_id. "模版code
    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail_new'
                           zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败'
                           zvalue1 = iv_spno
                           zvalue2 = lv_code
                           sendmsg = 'X'
                           fname = 'BULELIST' ).
      RETURN.
    ENDIF.
  ELSE.
    ls_detail = is_detail .
    lv_code = iv_code .
  ENDIF.

  lv_instance = iv_spno.

  "获取申请记录
  SELECT SINGLE * FROM ztint_market_req
    INTO @DATA(ls_ztint_req)
    WHERE instanceid = @iv_spno.

  "锁表
  CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
    EXPORTING
      mode_ztint_dp_pi_h = 'E'
      mandt              = sy-mandt
      instanceid         = lv_instance.

  IF sy-subrc <> 0.
    ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_special_coupon_new'
                                              eventdesc = '营销管理-申请特殊优惠券'
                                              status = 'E'
                                              message = '实例特殊优惠券锁表失败'
                                              key1 = iv_spno
                                              key2 = lv_code ).

    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
    RETURN.
  ENDIF.

  "抬头信息——这里第一次写该表
  ls_ztint_dp_pi_h = prepare_ztint_dp_pi_h( is_detail = ls_detail iv_type = iv_type ).

  "审批详情
  LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
    CLEAR ls_ztint_dp_pi_i.
    ls_ztint_dp_pi_i = prepare_ztint_dp_pi_i( iv_spno = iv_spno is_content = ls_content ).
    APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.  "这里只是存储，没有像发金币一样动态模版数据
  ENDLOOP.

  "获取发起人名称
  SELECT SINGLE username
    INTO @DATA(lv_username)
    FROM ztint_user_inf
    WHERE qywxuserid = @ls_detail-info-applyer-userid.
  IF lv_username IS INITIAL.
    lv_username = '企业微信'.
  ENDIF.

  "商品接口  获取品类
  DATA:lv_catagoryid TYPE string.
  lv_catagoryid = 'group_1'.
  DATA:lt_businesscatagoryid  TYPE zprod_t_businesscatagorys.

  DATA(lo_product) = NEW zcl_icec_product_api( ).
  DATA: lv_msg TYPE bapiret2.
  CALL METHOD lo_product->get_catagorys_firstcatagory
    EXPORTING
      iv_catagoryid         = lv_catagoryid
    IMPORTING
      ev_msg                = lv_msg
      et_businesscatagoryid = lt_businesscatagoryid.  "商品品类集合

  IF lv_msg-type = 'E'.
    ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_special_coupon_new'
                                              eventdesc = '营销管理-申请特殊优惠券'
                                              status = 'E'
                                              message = '调用接口特殊优惠券获取品类范围失败'
                                              key1 = iv_spno
                                              key2 = lv_code ).

    "审批抬头表
    ls_ztint_dp_pi_h-sendstatus = 'N'."发放失败
    ls_ztint_dp_pi_h-sendmsg = '获取品类范围失败'.

    "信息表
    ls_ztint_req-msg = '获取品类范围失败'.
    ls_ztint_req-status = 'APPROVE'." 审批状态 - 已审批
    ls_ztint_req-zupd_bdate = sy-datum.
    ls_ztint_req-zupd_btime = sy-uzeit.
    ls_ztint_req-zupd_uname = '企业微信'.

    "推送内部人员处理
    CLEAR lv_date_s.
    CLEAR lv_content.
    lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
    lv_alert_content = '开思助手 \n '   && '营销管理-有特殊优惠券申请审批获取品类范围失败,请关注! \n '
    && '实例号：' && iv_spno && ' \n '
    && '流程code：' && lv_code && ' \n '
    && lv_date_s.

    lv_fname = 'DPAPPLYSPECIALCOUPON'.
    lv_title =  '开思助手'.
    lv_msg_type = 'text'.

    CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
      EXPORTING
        iv_content = lv_alert_content
        iv_fname   = lv_fname.

  ELSE.  "调用接口发放优惠券

    DATA:lv_catagory TYPE string.

    "适用品类
    ls_send-category = 'EC_CATEGORY' .

    "品类
    LOOP AT lt_businesscatagoryid INTO DATA(ls_businesscatagoryid). "商品品类集合

      IF ls_ztint_req-categoryid = 'B'. "B的时候仅限油品
        SEARCH ls_businesscatagoryid-businesscatagoryname FOR '油品'.
        IF sy-subrc NE 0.
          CONTINUE.
        ENDIF.
      ENDIF.

      IF ls_ztint_req-categoryid = 'C'. "C的时候排除掉油品、化学品
        SEARCH ls_businesscatagoryid-businesscatagoryname FOR '油品'.
        IF sy-subrc = 0.
          CONTINUE.
        ENDIF.
      ENDIF.

      IF lv_catagory IS INITIAL .
        lv_catagory  = ls_businesscatagoryid-businesscatagoryid.
      ELSE.
        lv_catagory = lv_catagory && ',' && ls_businesscatagoryid-businesscatagoryid.
      ENDIF.

    ENDLOOP.

    lv_catagory = lv_catagory && ',' &&  'OTHER_CATEGORY'.

    ls_send-category_condition = lv_catagory.
    ls_send-product_category   = lv_catagory.

    "限定品牌brand
    IF ls_ztint_req-brandid IS NOT INITIAL . "新品导入 ，品牌新客 -限制品牌
      ls_send-product_brand = ls_ztint_req-brandid .
    ENDIF.

    "公司ID
    ls_send-company_id = ls_ztint_req-companyid .
    ls_send-company_id = ls_ztint_req-companyid .
    ls_send-company_id = ls_ztint_req-companyid .

    "费用归属
    ls_send-cost_ascription_type = 'PERSON'.

    "优惠券类型
    IF ls_ztint_req-coupontype = 'A01' ."满减券

      "优惠券面值  四舍五入
      ls_send-coupon_amount = round( val = ls_ztint_req-offermoney dec = 0 )."满减券面值
      ls_send-coupon_type = 'REACH_AMOUNT_REDUCE'."满减券

      "优惠券名称
      lv_orderamount = ls_ztint_req-orderamountmin.    "订单要求金额1000
      lv_discount    = ls_ztint_req-offermoney.       "满1000减50金额
      lv_desc = |满{ lv_orderamount }减{ lv_discount }|.

    ELSEIF ls_ztint_req-coupontype = 'A02' ."折扣券

      "优惠券面值
      ls_send-coupon_amount = CONV ty_p( ls_ztint_req-discountratio ).""折扣券的折扣值9.8
      ls_send-coupon_type = 'DISCOUNT'."折扣券

      lv_orderamount = ls_ztint_req-orderamountmin.    "订单要求金额1000
      "优惠券名称：“满【订单金额要求】- {【折扣值】*10 } 折（最高【折扣券上限】）

      DATA(lv_p) = ls_ztint_req-discountratio  * 10 . " 9.8 * 10
      lv_desc = |满{ lv_orderamount }-{ lv_p }折(最高{ ls_ztint_req-discountmax })|."折扣券名称

      ""折扣上限金额
      ls_send-discount_ceiling_amount = ls_ztint_req-discountmax.
    ENDIF.

    "优惠券名称
    ls_send-name = lv_desc .

    "最低要求的订单金额
    ls_send-requirement_amount = ls_ztint_req-orderamountmin .

    "订单类型
    "1商城订单 2询价订单
    IF ls_ztint_req-ordertype = '2'.
      ls_send-order_type = 'CUSTOMIZE_INQUIRY,COMMON_INQUIRY,DISTRIBUTION_INQUIRY'.
    ELSEIF ls_ztint_req-ordertype = '1'.
      ls_send-order_type = 'MALL'.
    ENDIF.

    "发放人
    ls_send-created_by = lv_username.

    "支付方式
    ls_send-payment = 'CASH,LOAN,CREDIT'.

    "可用渠道
    ls_send-available_channel = 'PC,APP'.

    "限定店铺 - 申请类型为新品导入时，限定店铺，其他为ALL
    DATA:lt_storeid TYPE TABLE OF ztint_ml_conf,
         lv_where   TYPE string.
    IF ls_ztint_req-reqtype = 'C' ."新品导入时

      IF ls_ztint_req-mltype = 'C4'. "品牌新客-取配置表
        SELECT storeid
          INTO CORRESPONDING FIELDS OF TABLE @lt_storeid
          FROM ztint_ml_conf
          WHERE brandid = @ls_ztint_req-brandid.
      ELSE. "C1 严选新客 C2共享仓新客 C3易损件新客

        lv_where = SWITCH #( ls_ztint_req-mltype
                              WHEN  'C1'  THEN |ZISMONO IN ('A','D')|
                              WHEN  'C2'  THEN |ISYUN EQ '1'|
                              WHEN  'C3'  THEN |ISFIRSTVULN EQ '1'|
                              ).

        SELECT productstoreid AS storeid
           INTO CORRESPONDING FIELDS OF TABLE @lt_storeid
           FROM ztint_venstore_f
           WHERE yearmonth = @sy-datum(6)  "取发放时点的店铺
           AND (lv_where).

      ENDIF.

      "取限制店铺的集合 X1,X2,X3
      SORT lt_storeid BY storeid .
      DELETE ADJACENT DUPLICATES FROM lt_storeid COMPARING storeid .

      DATA(lv_store) = REDUCE string(
                 INIT x = ||
                 FOR wa IN lt_storeid
                 NEXT x = COND #( WHEN x = '' THEN |{ wa-storeid }|
                                  ELSE |{ x },{ wa-storeid }| )
                                     ) .
      ls_send-store_codes = lv_store.
    ELSE.
      "其余的全店铺
      ls_send-store_codes = 'ALL'.
    ENDIF.

    "备注 remark
    SELECT SINGLE  reqname INTO @ls_send-remark
      FROM ztint_reqtype
      WHERE reqtype = @ls_ztint_req-reqtype .

    ls_send-attach_type = 'ALL'. "平台商家


    "有效期
    DATA: lv_begin TYPE sy-datum,
          lv_end   TYPE sy-datum.

    lv_begin = sy-datum.
    lv_end = sy-datum + 7.
    ls_send-validity_begin_date = |{ lv_begin DATE = ISO }|.
    ls_send-validity_end_date   = |{ lv_end DATE = ISO }|.

    "获取要发送的json串
    CALL FUNCTION 'Z_FMBC_GET_JSON'
      EXPORTING
        iv_data = ls_send
      IMPORTING
        ev_json = lv_body.

*-考虑多张优惠券的发放逻辑
*只有全部成功时，才算成功，有一张不成功即为失败

    "调用发放优惠券接口
    DATA:lv_id TYPE string.
    DATA:lv_cnt   TYPE i , "需要发放的次数
         lv_total TYPE i . "总计发放成功的次数
    lv_cnt = ls_ztint_req-couponnum + ( -1 ) * ls_ztint_req-couponsucess . " 还需要发送的 = 需要发放的 - 已成功发放的
    IF iv_number IS NOT INITIAL."手动发放
     lv_cnt = iv_number.
    ENDIF.
    CLEAR lv_successnum.
    DO lv_cnt TIMES. "
      DATA(lo_coupon) = NEW zcl_icec_coupon_api( ).
      CALL METHOD lo_coupon->send_special_coupon
        EXPORTING
          iv_data = lv_body
        IMPORTING
          ev_msg  = lv_msg
          ev_id   = lv_id. "发放成功时-优惠券id
      IF lv_msg-type = 'E'.
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_special_coupon_new'
                                                  eventdesc = '营销管理-申请特殊优惠券'
                                                  status = 'E'
                                                  message = '调用接口发放特殊优惠券失败'
                                                  key1 = iv_spno
                                                  key2 = lv_code ).
      ELSE.
        "成功后计数 + 1
        ls_ztint_req-couponsucess = ls_ztint_req-couponsucess + 1 .
        lv_successnum = lv_successnum + 1.

        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_special_coupon_new'
                                                  eventdesc = '营销管理-申请特殊优惠券'
                                                  status = 'S'
                                                  message = '调用发放特殊优惠券成功'
                                                  key1 = iv_spno
                                                  key2 = lv_code
                                                  key3 = lv_id  ).  "成功的记录一下发放的优惠券id
      ENDIF.
      MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.      "钉钉审批已审批log
    ENDDO.
    iv_successnum = lv_successnum."接口出参返回具体发放成功数量
    IF ls_ztint_req-couponnum NE ls_ztint_req-couponsucess . "需要发送的张数 不等于 已成功发送的张数 ，定义为失败
      "审批抬头表
      IF ls_ztint_req-couponsucess NE 0.
        ls_ztint_dp_pi_h-sendstatus = 'P'."部分发放
      ELSE.
        ls_ztint_dp_pi_h-sendstatus = 'N'."发放失败
      ENDIF.

      ls_ztint_dp_pi_h-senddate = sy-datum.
      ls_ztint_dp_pi_h-sendtime = sy-uzeit.
      ls_ztint_dp_pi_h-sendmsg  = lv_msg-message.

      "营销管理主信息表
      ls_ztint_req-msg    = lv_msg-message.
      ls_ztint_req-status = 'APPROVE'."已审批
      IF ls_ztint_req-couponsucess NE 0.
        ls_ztint_req-sendstatus = 'P'."部分发放
        ls_ztint_req-senddate = sy-datum."发放日期
      ELSE.
        ls_ztint_req-sendstatus = 'N'."发放失败
      ENDIF.
      ls_ztint_req-zupd_bdate = sy-datum.
      ls_ztint_req-zupd_btime = sy-uzeit.
      ls_ztint_req-zupd_uname = '企业微信'.

      "推送助手值班人员处理
      CLEAR lv_date_s.
      CLEAR lv_content.
      lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

      lv_alert_content = '开思助手 \n '  && ls_ztint_dp_pi_h-title  && '审批后调用电商接口失败,请关注! \n '
      && '实例号：' && ls_ztint_dp_pi_h-processinstanceid && ' \n '
      && '流程code：' && ls_ztint_dp_pi_h-processcode && ' \n '
      && lv_date_s.

      lv_fname = 'DPAPPLYSPECIALCOUPON'.
      lv_title =  '开思助手'.
      lv_msg_type = 'text'.

      CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
        EXPORTING
          iv_content = lv_alert_content
          iv_fname   = lv_fname.

    ELSE .
      "审批抬头表
      ls_ztint_dp_pi_h-sendstatus = 'Y'."发放成功
      ls_ztint_dp_pi_h-sendmsg = '特殊优惠券发放成功'.

      "营销管理主信息表
      ls_ztint_req-msg = '特殊优惠券发放成功'.
      ls_ztint_req-status = 'APPROVE'."已审批
      ls_ztint_req-sendstatus = 'Y'."发放成功
      ls_ztint_req-senddate = sy-datum."发放日期
      ls_ztint_req-zupd_bdate = sy-datum.
      ls_ztint_req-zupd_btime = sy-uzeit.
      ls_ztint_req-zupd_uname = '企业微信'.
      ls_ztint_req-coupouid = lv_id.  "记录一个即可

      IF ls_ztint_req-userid IS NOT INITIAL. "申请人userid

        "通知申请人额度生效
        CLEAR lv_date_s.
        CLEAR lv_content.
        lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
        lv_content =   '开思助手 \n '   && '您申请的【特殊优惠券】已发放成功，请知悉！ \n '
        && '客户名称：' && ls_ztint_req-companyname && ' \n '
        && '客户代码：' && ls_ztint_req-companycode && ' \n '
        && '优惠券：' && lv_desc && ' \n '  "满1000减50
        && lv_date_s.

        CLEAR ls_user_content.
        ls_user_content-content = lv_content.
        ls_user_content-userid = ls_ztint_req-userid.
        ls_user_content-orderno = |{ ls_ztint_req-companycode }|.
        ls_user_content-orderdesc = |{ ls_ztint_req-companyname }|.
        ls_user_content-firstcatgory  = '客户管理'.
        ls_user_content-secondcatgory = '特殊优惠券申请审批'.
        APPEND ls_user_content TO lt_user_content.
      ENDIF.
    ENDIF.
  ENDIF .

  "给值到调用点
  is_dp_pi_h  = ls_ztint_dp_pi_h .
  "更新表
  MODIFY ztint_market_req FROM ls_ztint_req.       "审批流程主信息表
  MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.      "钉钉审批已审批抬头
  MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i."钉钉审批已审批详情

  "解锁表
  CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
    EXPORTING
      mode_ztint_dp_pi_h = 'E'
      mandt              = sy-mandt
      instanceid         = lv_instance.

*   推送通知
  IF lt_user_content IS NOT INITIAL.
    lv_title =  '开思助手'.
    lv_msg_type = 'text'.
    CONDENSE lv_url NO-GAPS .

    CALL FUNCTION 'Z_FMQYWX_CASSINT_PUSH_MESSAGE'
*      IN UPDATE TASK
      EXPORTING
        iv_title    = lv_title
        iv_msgtype  = lv_msg_type
        iv_url      = lv_url
      TABLES
        it_userlist = lt_user_content.
  ENDIF.

ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPLY_PURCHASEEXC_COMPENSATE
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD apply_purchaseexc_compensate.

    DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h,
          ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
          lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i,
          ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
          lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l,
          ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
    DATA:lt_user_content TYPE  zsint_push_userlist_tab,
         ls_user_content TYPE  zsint_push_userlist.
    DATA:lv_body          TYPE string,
         lv_instance      TYPE zde_intprocessinstanceid,
         lv_alert_content TYPE string,
         lv_fname         TYPE string,
         lv_url           TYPE string,
         lv_title         TYPE string,
         lv_msg_type      TYPE string,
         lv_content       TYPE string,
         lv_date_s        TYPE string.

    DATA:lv_orderid       TYPE string,
         lv_abnormalstage TYPE string,
         lv_providereason TYPE string,
         lv_remark        TYPE string,
         lv_provideway    TYPE string,
         lv_number        TYPE string,
         lv_finishstamp   TYPE string.
    DATA:lv_status TYPE ztint_cuspep_h-status.
    DATA:lt_ztint_dp_conpe_h TYPE STANDARD TABLE OF ztint_dp_conpe_h,
         ls_ztint_dp_conpe_h LIKE LINE OF lt_ztint_dp_conpe_h,
         lt_ztint_dp_conpe_i TYPE STANDARD TABLE OF ztint_dp_conpe_i,
         ls_ztint_dp_conpe_i LIKE LINE OF lt_ztint_dp_conpe_i.

    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
    DATA(lv_code) = ls_detail-info-template_id.

    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
                           sendmsg = 'X' fname = 'BULELIST' ).
      RETURN.
    ENDIF.

    lv_instance = iv_spno.
    "审批结果
    CASE iv_type.
      WHEN 'agree'. "审批通过
        "判断这个实例是否已经加过
        "实例已发放记录日志
        SELECT SINGLE * FROM ztint_dp_conpe_h
          INTO CORRESPONDING FIELDS OF @ls_ztint_dp_conpe_h
          WHERE processinstanceid =  @iv_spno.
        IF ls_ztint_dp_conpe_h-sendstatus = 'Y'. "已发放
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_purchase_compensate'
                    eventdesc = '发放商家处罚函' status = 'E' message = '实例已发放过商家处罚函'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.

        "取配置的流程字段
        SELECT * FROM ztint_dp_field INTO TABLE @DATA(lt_ztint_dp_field)
          WHERE processcode = @lv_code.
        SORT lt_ztint_dp_field BY dingname.

        "取配置的流程字段值
        SELECT * FROM ztint_dp_value INTO TABLE @DATA(lt_ztint_dp_value)
          WHERE processcode = @lv_code.

        "抬头信息
        DATA: lv_timestamp TYPE p.
        "没增加过
        "抬头信息赋值
        ls_ztint_dp_conpe_h-processinstanceid = ls_detail-info-sp_no.
        ls_ztint_dp_conpe_h-processcode = ls_detail-info-template_id.
        ls_ztint_dp_conpe_h-title = ls_detail-info-sp_name.
*        dp_pi_h-businessid = ls_detail-business_id.
        ls_ztint_dp_conpe_h-zresult = iv_type.
        lv_timestamp  = ls_detail-info-apply_time."创建时间
        IF lv_timestamp > 0.
          zcl_cassint_formatter=>convert_unix_timestamp_to_abap(
            EXPORTING unixsecs = lv_timestamp
            IMPORTING ev_date = ls_ztint_dp_conpe_h-createdate ev_time = ls_ztint_dp_conpe_h-createtime ).
          ls_ztint_dp_conpe_h-createstamp = lv_timestamp.
          CONDENSE ls_ztint_dp_conpe_h-createstamp.
        ENDIF.
        CLEAR lv_timestamp.

        DATA(lv_notes) = lines( ls_detail-info-sp_record )."多少审批节点
**        DATA(lv_time) = lines( ls_detail-info-sp_record[ lv_notes ]-details )."每个审批节点几个人依次审批
**        lv_timestamp = VALUE #( ls_detail-info-sp_record[ lv_notes ]-details[ lv_time ]-sptime OPTIONAL ).
        DATA(lt_time) = ls_detail-info-sp_record[ lv_notes ]-details."每个审批节点几个人依次审批
        SORT lt_time BY sptime DESCENDING.
        READ TABLE lt_time INTO DATA(ls_time) INDEX 1.
        lv_timestamp =    ls_time-sptime.
        IF lv_timestamp > 0.
          zcl_cassint_formatter=>convert_unix_timestamp_to_abap(
            EXPORTING unixsecs = lv_timestamp
            IMPORTING ev_date = ls_ztint_dp_conpe_h-finishdate ev_time = ls_ztint_dp_conpe_h-finishtime ).
          ls_ztint_dp_conpe_h-finishstamp = lv_timestamp.
          CONDENSE ls_ztint_dp_conpe_h-finishstamp.
        ENDIF.
        CLEAR lv_timestamp.
        ls_ztint_dp_conpe_h-status = 'finish'.
        ls_ztint_dp_conpe_h-originatordeptid = ls_detail-info-applyer-partyid.
        ls_ztint_dp_conpe_h-originatoruserid = ls_detail-info-applyer-userid.
        ls_ztint_dp_conpe_h-senddate = sy-datum.
        ls_ztint_dp_conpe_h-sendtime = sy-uzeit.

        "审批详情
        LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
          CLEAR ls_ztint_dp_conpe_i.
          ls_ztint_dp_conpe_i-processinstanceid = iv_spno.
          ls_ztint_dp_conpe_i-posnr = sy-tabix.
          ls_ztint_dp_conpe_i-name = VALUE #( ls_content-title[ 1 ]-text OPTIONAL ).
          CASE ls_content-control.
            WHEN 'Text' OR 'Textarea'.
              ls_ztint_dp_conpe_i-value = ls_content-value-text.
            WHEN 'Number'.
              ls_ztint_dp_conpe_i-value = ls_content-value-new_number.
            WHEN 'Money'.
              ls_ztint_dp_conpe_i-value = ls_content-value-new_money.
            WHEN 'Selector'.
              ls_ztint_dp_conpe_i-value = VALUE #( ls_content-value-selector-options[ 1 ]-value[ 1 ]-text OPTIONAL ).
            WHEN  OTHERS.
          ENDCASE.
          APPEND ls_ztint_dp_conpe_i TO lt_ztint_dp_conpe_i.

          READ TABLE lt_ztint_dp_field INTO DATA(ls_ztint_dp_field) WITH KEY dingname = ls_ztint_dp_conpe_i-name BINARY SEARCH.
          IF sy-subrc = 0.
            CASE ls_ztint_dp_field-name.
              WHEN 'orderid'. "订单编号
                lv_orderid = ls_ztint_dp_conpe_i-value.
*            WHEN 'companycode'."维修厂代码
*            WHEN 'companyname'."维修厂名称
              WHEN 'abnormalstage'."异常阶段
                lv_abnormalstage = ls_ztint_dp_conpe_i-value.
              WHEN 'providereason'."补偿原因
                lv_providereason = ls_ztint_dp_conpe_i-value.
              WHEN 'remark'."详细原因
                lv_remark = ls_ztint_dp_conpe_i-value.
              WHEN 'provideway'."补偿方式
                lv_provideway = ls_ztint_dp_conpe_i-value.
              WHEN 'number'."补偿金额
                lv_number = ls_ztint_dp_conpe_i-value.
              WHEN OTHERS.
            ENDCASE.
          ENDIF.
          CLEAR ls_ztint_dp_field.
        ENDLOOP.
        CONDENSE lv_number.
        CHECK lv_number IS NOT INITIAL.
        DATA:lv_amt TYPE zde_intamount.
        TRY .
            lv_amt = lv_number.
          CATCH cx_sy_conversion_no_number INTO DATA(lo_num).
        ENDTRY.
        CHECK lv_amt > 0.
        IF lv_orderid IS NOT INITIAL.
          SELECT SINGLE a~productstoreid INTO @DATA(lv_productstoreid)
            FROM ztint_ven_inf AS a INNER JOIN zticec_order_h AS b ON a~productstoreid = b~productstoreid
            WHERE b~orderid = @lv_orderid.

*        "获取发起人名称
          SELECT SINGLE username,telf INTO @DATA(ls_user) FROM ztint_user_inf
            WHERE qywxuserid = @ls_detail-info-applyer-userid.
        ENDIF.

        REPLACE ALL OCCURRENCES OF '\' IN lv_providereason WITH ''.
        CONDENSE lv_providereason.
        IF lv_providereason = '报价错误' OR
          lv_providereason = '未按约定时间发货' OR
          lv_providereason = '发错地址' OR
          lv_providereason = '漏发/错发货' OR
          lv_providereason = '未贴开思标签' OR
          lv_providereason = '下单后无货'.

          IF ls_ztint_dp_conpe_h-finishdate IS NOT INITIAL.
            zcl_cassint_formatter=>convert_abap_to_timestamp(
              EXPORTING
                date        =  ls_ztint_dp_conpe_h-finishdate   " ABAP 系统字段：应用服务器的当前日期
                time        =  ls_ztint_dp_conpe_h-finishtime   " ABAP 系统字段：应用服务器的当前时间
              RECEIVING
                r_timestamp = lv_finishstamp
            ).
          ENDIF.
          "准备调用接口
          "操作人
          CLEAR lv_body.
          lv_body = '{ ' &&
                    |"storeId":"{ lv_productstoreid }",| &&
                    |"historyCustomManagerName":"{ ls_user-username }",| &&
                    |"historyCustomManagerPhone":"{ ls_user-telf }",| &&
                    |"orderId":"{ lv_orderid }",| &&
                    |"exceptionStage":"{ lv_abnormalstage }",| &&
                    |"penaltyReason":"{ lv_providereason }",| &&
                    |"penaltyAmount":"{ lv_number }",| &&
                    |"financeApprovalTime":"{ lv_finishstamp }"| &&
                    '}'.

          NEW zcl_icec_merchant_api( )->send_merchant_punishinfo(
              EXPORTING iv_body = lv_body IMPORTING ev_msg = DATA(ls_msg) ).
        ENDIF.
        IF ls_msg-type = 'S'.
          ls_ztint_dp_conpe_h-sendstatus = 'Y'. "发放
          ls_ztint_dp_conpe_h-sendmsg = '调用接口发放商家处罚函成功'.
          "记录日志
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_purchase_compensate'
                    eventdesc = '发放商家处罚函' status = 'S' message = '调用接口发放商家处罚函成功'
                    key1 = iv_spno key2 = lv_code ).

        ELSEIF ls_msg-type = 'E'.
          ls_ztint_dp_conpe_h-sendstatus = 'N'. "没发放
          ls_ztint_dp_conpe_h-sendmsg = '调用接口发放商家处罚函失败'.
          "记录日志
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_purchase_compensate'
                    eventdesc = '发放商家处罚函' status = 'E' message = '调用接口发放商家处罚函失败'
                    key1 = iv_spno key2 = lv_code ).

          "推送内部人员处理
          CLEAR lv_date_s.
          CLEAR lv_content.
          lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

          lv_alert_content = '开思助手 \n '  && ls_ztint_dp_conpe_h-title  && '审批后调用接口发送商家处罚函信息失败,请关注! \n '
             && '实例号：' && ls_ztint_dp_conpe_h-processinstanceid && ' \n '
             && '流程code：' && ls_ztint_dp_conpe_h-processcode && ' \n '
             && lv_date_s.

          lv_fname = 'DPAPPLYEXCEPTCOMPE'.
          lv_title =  '开思助手'.
          lv_msg_type = 'text'.

          CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
            EXPORTING
              iv_content = lv_alert_content
              iv_fname   = lv_fname.
        ENDIF.

        MODIFY ztint_dp_conpe_h FROM ls_ztint_dp_conpe_h.
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        MODIFY ztint_dp_conpe_i FROM TABLE lt_ztint_dp_conpe_i.
      WHEN 'refuse'."审批拒绝
      WHEN 'terminate'."审批撤销
      WHEN OTHERS.
    ENDCASE.
    lv_status = COND #( WHEN iv_type EQ 'agree' THEN 'APPROVE'
                        WHEN iv_type EQ 'refuse' THEN  'UNAPPROVE'
                        WHEN iv_type EQ 'terminate' THEN 'CANCELED' ELSE '' ).
    "更新表状态
    SELECT SINGLE guid INTO @DATA(l_guid) FROM ztint_dp_inf WHERE instanceid = @lv_instance.
    CHECK sy-subrc EQ 0.
    "锁表
    CALL FUNCTION 'ENQUEUE_EZ_INT_DP_INF'
      EXPORTING
        mode_ztint_dp_inf = 'E'
        mandt             = sy-mandt
        guid              = l_guid.
    IF sy-subrc <> 0.
      ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'compenstate_update'
                eventdesc = '客户异常采购补偿申请审批流状态更新' status = 'E' message = '更新审批状态锁表失败'
                key1 = iv_spno key2 = lv_code ).
      MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      RETURN.
    ENDIF.

    SELECT SINGLE * FROM ztint_cuspep_h INTO @DATA(ls_cuspep_h) WHERE guid = @l_guid.
    IF sy-subrc NE 0.
      UPDATE ztint_dp_inf
         SET status = lv_status
             msg = 'finish'
             zupd_bdate = sy-datum
             zupd_btime = sy-uzeit
             zupd_uname = sy-uname
        WHERE guid = l_guid.
    ELSE.
      UPDATE ztint_dp_inf
           SET status = lv_status
               msg = 'finish'
               zupd_bdate = sy-datum
               zupd_btime = sy-uzeit
               zupd_uname = sy-uname
          WHERE guid = l_guid.
      UPDATE ztint_cuspep_h
           SET status = lv_status
               msg = 'finish'
               businessid = iv_spno
               zupd_bdate = sy-datum
               zupd_btime = sy-uzeit
               zupd_uname = sy-uname
          WHERE guid = l_guid.
    ENDIF.
    CALL FUNCTION 'DEQUEUE_EZ_INT_DP_INF'
      EXPORTING
        mode_ztint_dp_inf = 'E'
        mandt             = sy-mandt
        guid              = l_guid.

  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPLY_STORE_OPEN
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD apply_store_open.

    DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h,
          ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
          lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i,
          ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
          lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l,
          ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
    DATA:lt_user_content TYPE  zsint_push_userlist_tab,
         ls_user_content TYPE  zsint_push_userlist.
    DATA:lv_body          TYPE string,
         lv_instance      TYPE zde_intprocessinstanceid,
         lv_alert_content TYPE string,
         lv_fname         TYPE string,
         lv_url           TYPE string,
         lv_title         TYPE string,
         lv_msg_type      TYPE string,
         lv_content       TYPE string,
         lv_date_s        TYPE string.

    DATA lv_status TYPE ztint_vensapply-status.
    DATA lv_approvestatus TYPE ztint_storeapply-approvalstatus.
    DATA lv_msg1 TYPE ztint_vensapply-msg.
    DATA lv_msg TYPE bapiret2.


    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
    DATA(lv_code) = ls_detail-info-template_id.

    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
                           sendmsg = 'X' fname = 'BULELIST' ).
      RETURN.
    ENDIF.


    "审批结果
    CASE iv_type.
      WHEN 'agree' OR 'refuse'. "审批通过
        "查找申请记录
        SELECT SINGLE * FROM ztint_vensapply INTO @DATA(ls_vensapply)
             WHERE instanceid = @iv_spno.
        IF sy-subrc NE 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_store_open'
                    eventdesc = '店铺开设申请' status = 'E' message = '未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.
        "判断是否已经处理过
        SELECT SINGLE * FROM ztint_dp_pi_h INTO CORRESPONDING FIELDS OF @ls_ztint_dp_pi_h
          WHERE processinstanceid = @iv_spno.
        IF ls_ztint_dp_pi_h-sendstatus = 'Y'. "处理过
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_store_open'
                    eventdesc = '店铺开设申请' status = 'E' message = '实例店铺开设申请已处理过'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.

        lv_instance = iv_spno.
        "锁表
        CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.
        IF sy-subrc <> 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_store_open'
                    eventdesc = '店铺开设申请' status = 'E' message = '实例店铺开设申请锁表失败'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.

        "抬头信息
        ls_ztint_dp_pi_h = prepare_ztint_dp_pi_h( is_detail = ls_detail iv_type = iv_type ).
        "审批详情
        LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
          CLEAR ls_ztint_dp_pi_i.
          ls_ztint_dp_pi_i = prepare_ztint_dp_pi_i( iv_spno = iv_spno is_content = ls_content ).
          APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.
        ENDLOOP.


        "调用平台商家接口同步审核状态
        DATA(lo_merchant) = NEW zcl_icec_merchant_api( ).
        DATA(lv_exclusive) = COND #( WHEN ls_vensapply-isexclusive EQ 'Y' THEN '1'
                                    WHEN ls_vensapply-isexclusive EQ 'N' THEN '0' ).

        SELECT SINGLE a~userid,c~username,c~telf INTO @DATA(ls_manager) FROM ztint_ven_user AS a
          INNER JOIN ztint_vendor AS b ON a~venid = b~venid
          INNER JOIN ztint_user_inf AS c ON a~userid = c~userid
          INNER JOIN ztint_storeapply AS d ON b~vendorcode = d~vendorcode
          WHERE d~applyguid = @ls_vensapply-applyguid AND a~isdelete EQ ''
          AND a~ispre EQ '' AND a~usertype EQ '1'.

        "获取处理人信息 "获取发起人名称
        SELECT SINGLE userid,username INTO @DATA(ls_operator) FROM ztint_user_inf
          WHERE qywxuserid = @ls_detail-info-applyer-userid.

        DATA(lv_storetype) = COND #( WHEN ls_vensapply-storetype EQ 'DISTRIBUTO' THEN 'DISTRIBUTOR' ELSE ls_vensapply-storetype ).
        lv_body = '{' &&
                    |"storeId":"{ ls_vensapply-productstoreid }","storeName":"{ ls_vensapply-storename }",| &&
                    |"storeType":"{ lv_storetype }","exclusive":"{ lv_exclusive } ",| &&
                    |"auditState":"AUDIT_SUCCESS","remark":"","auditor":"{ ls_operator-userid }",| &&
                    |"devManager":"{ ls_manager-userid }","devCellphone":"{ ls_manager-telf }"| && '}'.
        lo_merchant->update_storeapply_audit( EXPORTING iv_body = lv_body IMPORTING ev_msg = DATA(ls_msg) es_vensapply = DATA(ls_vensapply_inf) ).

        IF ls_vensapply_inf-storeid IS NOT INITIAL."成功处理
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_store_open'
                    eventdesc = '店铺开设申请' status = 'S' message = '审批后调用电商接口成功'
                    key1 = iv_spno key2 = lv_code ).
          "审批抬头表
          ls_ztint_dp_pi_h-sendstatus = 'Y'."已处理过
          ls_ztint_dp_pi_h-sendmsg = '成功'.

          lv_status = COND #( WHEN iv_type EQ 'agree' THEN 'APPROVE' ELSE 'UNAPPROVE').
          lv_approvestatus = COND #( WHEN iv_type EQ 'agree' THEN 'PASSED' ELSE 'FAILED' ).
          lv_msg1 = '成功'.
          "更新店铺开设申请记录
          UPDATE ztint_vensapply SET status = lv_status
                                    msg =  lv_msg1
                                    zupd_bdate = sy-datum
                                    zupd_btime = sy-uzeit
                                    zupd_uname = sy-uname
                              WHERE guid = ls_vensapply-guid.
          "更新店铺开设申请钉钉审批记录
          UPDATE ztint_storeapply SET approvalstatus = lv_approvestatus
                                      zupd_bdate = sy-datum
                                      zupd_btime = sy-uzeit
                                      zupd_uname = sy-uname
                                WHERE applyguid = ls_vensapply-applyguid.
          "更新审批抬头表
          UPDATE ztint_dp_inf SET status = lv_status
                                   msg =  lv_msg1
                                   zupd_bdate = sy-datum
                                   zupd_btime = sy-uzeit
                                   zupd_uname = sy-uname
                             WHERE guid = ls_vensapply-guid.

*****记录操作日志
          lv_content = COND #( WHEN iv_type EQ 'agree' THEN |{ ls_operator-username }已批准店铺({ ls_vensapply-storename })的开设申请|
                               ELSE |{ ls_operator-username }已拒绝店铺({ ls_vensapply-storename })的开设申请| ).
          TRY .
              DATA(ls_opera_log) = VALUE ztint_opera_log( guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
                                                          fnam = 'M007' operatetion = 'O001' userid = ls_operator-userid
                                                          username = ls_operator-username objectid = ls_vensapply-venid
                                                          object = ls_vensapply-storename
                                                          content = lv_content operatedate = sy-datum operatetime = sy-uzeit ).
              MODIFY ztint_opera_log FROM ls_opera_log.
            CATCH cx_uuid_error.
          ENDTRY.

        ELSE."调用接口失败
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_store_open'
                    eventdesc = '店铺开设申请' status = 'E' message = '审批后调用电商接口失败'
                    key1 = iv_spno key2 = lv_code ).
          "审批抬头表
          ls_ztint_dp_pi_h-sendstatus = 'N'."处理失败
          ls_ztint_dp_pi_h-sendmsg = lv_msg-message.

          "推送内部人员处理
          CLEAR:lv_date_s,lv_content.
          lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
          lv_alert_content = '开思助手 \n '  && ls_ztint_dp_pi_h-title  && '审批后调用电商接口失败,请关注! \n '
             && '实例号：' && ls_ztint_dp_pi_h-processinstanceid && ' \n '
             && '流程code：' && ls_ztint_dp_pi_h-processcode && ' \n '
             && lv_date_s.

          lv_fname = 'DPAPPLYSTOREOPEN'.
          lv_title =  '开思助手'.
          lv_msg_type = 'text'.

          CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
            EXPORTING
              iv_content = lv_alert_content
              iv_fname   = lv_fname.
        ENDIF.

        MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i.

        "解锁
        CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.

      WHEN 'terminate'."审批撤销
        CLEAR ls_vensapply.
        SELECT SINGLE * FROM ztint_vensapply INTO CORRESPONDING FIELDS OF ls_vensapply
               WHERE instanceid = iv_spno.
        IF sy-subrc = 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_store_open'
                    eventdesc = '店铺开设申请' status = 'S' message = '更新店铺开设申请撤销'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.

          lv_status = 'CANCELED'.
          lv_approvestatus = 'TO_APPROVE'.
          lv_msg = '已撤销'.
          "更新店铺申请审批记录
          UPDATE ztint_vensapply SET status = lv_status
                                     msg =  lv_msg
                                     zupd_bdate = sy-datum
                                     zupd_btime = sy-uzeit
                                     zupd_uname = sy-uname
                               WHERE guid = ls_vensapply-guid.
          "更新商家店铺申请审批记录
          UPDATE ztint_storeapply SET approvalstatus = lv_approvestatus
                                      zupd_bdate = sy-datum
                                      zupd_btime = sy-uzeit
                                      zupd_uname = sy-uname
                                WHERE applyguid = ls_vensapply-applyguid.

          "更新审批抬头表
          UPDATE ztint_dp_inf SET status = lv_status
                                    msg =  lv_msg
                                    zupd_bdate = sy-datum
                                    zupd_btime = sy-uzeit
                                    zupd_uname = sy-uname
                              WHERE guid = ls_vensapply-guid.

          CLEAR:ls_opera_log,lv_content,ls_operator.
          SELECT SINGLE i~userid,i~username INTO @ls_operator FROM ztint_user_inf AS i
              INNER JOIN ztint_dp_inf AS f ON i~userid = f~userid
              WHERE f~guid = @ls_vensapply-guid.
*****记录操作日志
          lv_content = |{ ls_operator-username }已撤销店铺({ ls_vensapply-storename })的开设申请|.
          TRY .
              ls_opera_log = VALUE ztint_opera_log( guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
                                                    fnam = 'M007' operatetion = 'O001' userid = ls_operator-userid
                                                    username = ls_operator-username objectid = ls_vensapply-venid
                                                    object = ls_vensapply-storename
                                                    content = lv_content operatedate = sy-datum operatetime = sy-uzeit ).
              MODIFY ztint_opera_log FROM ls_opera_log.
            CATCH cx_uuid_error.
          ENDTRY.

        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_store_open'
                    eventdesc = '店铺开设申请' status = 'E' message = '店铺开设申请撤销未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ENDIF.
      WHEN OTHERS.
    ENDCASE.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPLY_TEMP_CLASS
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD apply_temp_class.

    DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h,
          ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
          lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i,
          ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
          lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l,
          ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
    DATA:lt_user_content TYPE  zsint_push_userlist_tab,
         ls_user_content TYPE  zsint_push_userlist.
    DATA:lv_body          TYPE string,
         lv_instance      TYPE zde_intprocessinstanceid,
         lv_alert_content TYPE string,
         lv_fname         TYPE string,
         lv_url           TYPE string,
         lv_title         TYPE string,
         lv_msg_type      TYPE string,
         lv_content       TYPE string,
         lv_date_s        TYPE string.

    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
    DATA(lv_code) = ls_detail-info-template_id.

    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
                           sendmsg = 'X' fname = 'BULELIST' ).
      RETURN.
    ENDIF.


    "审批结果
    CASE iv_type.
      WHEN 'agree'. "审批通过
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'blue_instanceid_not_found'
                  eventdesc = '申请蓝名单流程ID' status = 'E' message = '未找到对应的申请记录'
                  key1 = iv_spno key2 = lv_code ).

        "抬头信息
        ls_ztint_dp_pi_h = prepare_ztint_dp_pi_h( is_detail = ls_detail iv_type = iv_type ).
        "审批详情
        LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
          CLEAR ls_ztint_dp_pi_i.
          ls_ztint_dp_pi_i = prepare_ztint_dp_pi_i( iv_spno = iv_spno is_content = ls_content ).
          APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.
        ENDLOOP.

        "获取最后审批人信息
*        DATA(lv_notes) = lines( ls_detail-info-sp_record )."多少审批节点
*        DATA(lv_times) = lines( ls_detail-info-sp_record[ lv_notes ]-details )."每个审批节点几个人依次审批
*        DATA(lv_finishuser) = VALUE #( ls_detail-info-sp_record[ lv_notes ]-details[ lv_times ]-approver-userid OPTIONAL ).
*        IF lv_finishuser IS NOT INITIAL.
*          SELECT SINGLE username INTO @DATA(lv_username) FROM ztint_user_inf WHERE qywxuserid = @lv_finishuser.
*        ENDIF.
*        IF lv_username IS INITIAL.
*          lv_username = '企业微信'.
*        ENDIF.
*
*        "获取发起人名称
*        SELECT SINGLE username INTO @DATA(lv_username) FROM ztint_user_inf
*          WHERE qywxuserid = @ls_detail-info-applyer-userid.
*        IF lv_username IS INITIAL.
*          lv_username = '企业微信'.
*        ENDIF.


**          lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

**        CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
**          IN UPDATE TASK
**          EXPORTING
**            iv_content = lv_alert_content
**            iv_fname   = lv_fname.
**
**        CALL FUNCTION 'Z_FMQYWX_CASSINT_PUSH_MESSAGE'
**          IN UPDATE TASK
**          EXPORTING
**            iv_title    = lv_title
**            iv_msgtype = lv_msg_type
**            iv_url      = lv_url
**          TABLES
**            it_userlist = lt_user_content.
      WHEN 'refuse'."审批拒绝
      WHEN 'terminate'."审批撤销
      WHEN OTHERS.
    ENDCASE.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPLY_VENDOR_ACCESS
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD apply_vendor_access.

    DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h,
          ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
          lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i,
          ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
          lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l,
          ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
    DATA:lt_user_content TYPE  zsint_push_userlist_tab,
         ls_user_content TYPE  zsint_push_userlist.
    DATA:lv_body          TYPE string,
         lv_instance      TYPE zde_intprocessinstanceid,
         lv_alert_content TYPE string,
         lv_fname         TYPE string,
         lv_url           TYPE string,
         lv_title         TYPE string,
         lv_msg_type      TYPE string,
         lv_content       TYPE string,
         lv_date_s        TYPE string.

    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
    DATA(lv_code) = ls_detail-info-template_id.

    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
                           sendmsg = 'X' fname = 'BULELIST' ).
      RETURN.
    ENDIF.


    "审批结果
    CASE iv_type.
      WHEN 'agree'. "审批通过
        "查找申请记录
        SELECT SINGLE * FROM ztint_venaccess INTO @DATA(ls_ztint_venaccess)
          WHERE instanceid = @iv_spno.
        IF sy-subrc NE 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_access'
                    eventdesc = '商家准入申请' status = 'E' message = '未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.

        "判断是否已经处理过
        SELECT SINGLE * FROM ztint_dp_pi_h INTO CORRESPONDING FIELDS OF @ls_ztint_dp_pi_h
          WHERE processinstanceid = @iv_spno.
        IF ls_ztint_dp_pi_h-sendstatus = 'Y'. "处理过
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_access'
                    eventdesc = '商家准入申请' status = 'E' message = '实例商家准入申请已处理过'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.

        lv_instance = iv_spno.
        "锁表
        CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.
        IF sy-subrc <> 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_access'
                    eventdesc = '商家准入申请' status = 'E' message = '实例商家准入申请锁表失败'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.
        "抬头信息
        ls_ztint_dp_pi_h = prepare_ztint_dp_pi_h( is_detail = ls_detail iv_type = iv_type ).
        "审批详情
        LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
          CLEAR ls_ztint_dp_pi_i.
          ls_ztint_dp_pi_i = prepare_ztint_dp_pi_i( iv_spno = iv_spno is_content = ls_content ).
          APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.
        ENDLOOP.

        "审批抬头表
        ls_ztint_dp_pi_h-sendstatus = 'Y'."已处理过
        ls_ztint_dp_pi_h-sendmsg = '成功'.

        MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.
        MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i.

        "更新商家准入申请记录
        UPDATE ztint_venaccess SET status = 'APPROVE'
                                  msg =  '成功'
                                  zupd_bdate = sy-datum
                                  zupd_btime = sy-uzeit
                                  zupd_uname = sy-uname
                 WHERE guid = ls_ztint_venaccess-guid.

        "更新审批抬头表
        UPDATE ztint_dp_inf SET  status = 'APPROVE'
                                 msg =  '成功'
                                 zupd_bdate = sy-datum
                                 zupd_btime = sy-uzeit
                                 zupd_uname = sy-uname
                WHERE guid = ls_ztint_venaccess-guid.

        "更新线索信息准入申请状态
        SELECT SINGLE venid,venname,isgroupven INTO @DATA(ls_venid)
          FROM ztint_venaccess
          WHERE venid = @ls_ztint_venaccess-venid.
        IF ls_venid-venid IS NOT INITIAL.

          "获取最后审批人信息
          DATA(lv_notes) = lines( ls_detail-info-sp_record )."多少审批节点
*          DATA(lv_times) = lines( ls_detail-info-sp_record[ lv_notes ]-details )."每个审批节点几个人依次审批
*          DATA(lv_finishuser) = VALUE #( ls_detail-info-sp_record[ lv_notes ]-details[ lv_times ]-approver-userid OPTIONAL ).
          DATA(lt_time) = ls_detail-info-sp_record[ lv_notes ]-details."每个审批节点几个人依次审批
          SORT lt_time BY sptime DESCENDING.
          READ TABLE lt_time INTO DATA(ls_time) INDEX 1.
          DATA(lv_finishuser) = ls_time-approver-userid.
          IF lv_finishuser IS NOT INITIAL.
            SELECT SINGLE userid,username INTO @DATA(ls_opera_per) FROM ztint_user_inf WHERE qywxuserid = @lv_finishuser.
          ENDIF.

          UPDATE ztint_venclue_in SET accessstatus = '3' "以准入
                                      idstatus = '1'"账号待创建
                                      isgroupven = ls_venid-isgroupven
                                      zupd_bdate = sy-datum
                                      zupd_btime = sy-uzeit
                                      zupd_uname = sy-uname
                           WHERE venid = ls_venid-venid.
*****记录操作日志
          lv_content = |{ ls_opera_per-username }已批准商家线索({ ls_venid-venname })的准入申请|.
          TRY .
              DATA(ls_opera_log) = VALUE ztint_opera_log( guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
                                                          fnam = 'M007' operatetion = 'O001' userid = ls_opera_per-userid
                                                          username = ls_opera_per-username objectid = ls_venid-venid
                                                          object = ls_venid-venname
                                                          content = lv_content operatedate = sy-datum operatetime = sy-uzeit ).
              MODIFY ztint_opera_log FROM ls_opera_log.
            CATCH cx_uuid_error.
          ENDTRY.
        ENDIF.

        "解锁
        CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.

      WHEN 'refuse'."审批拒绝
        CLEAR ls_ztint_venaccess.
        SELECT SINGLE * FROM ztint_venaccess INTO CORRESPONDING FIELDS OF ls_ztint_venaccess
          WHERE instanceid = iv_spno.

        IF sy-subrc = 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_access'
                    eventdesc = '商家准入申请' status = 'S' message = '更新商家准入申请拒绝状态'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.

          "更新商家准入申请记录
          UPDATE ztint_venaccess SET status = 'UNAPPROVE'
                                    msg =  '商家准入申请被审批人拒绝'
                                    zupd_bdate = sy-datum
                                    zupd_btime = sy-uzeit
                                    zupd_uname = sy-uname
                   WHERE guid = ls_ztint_venaccess-guid.

          "更新审批抬头表
          UPDATE ztint_dp_inf SET status = 'UNAPPROVE'
                                    msg = '商家准入申请被审批人拒绝'
                                    zupd_bdate = sy-datum
                                    zupd_btime = sy-uzeit
                                    zupd_uname = sy-uname
                            WHERE guid = ls_ztint_venaccess-guid.

          "更新线索信息准入申请状态
          CLEAR:ls_venid,lv_content,ls_opera_log.
          SELECT SINGLE venid,venname INTO @ls_venid
            FROM ztint_venaccess
            WHERE venid = @ls_ztint_venaccess-venid.
          IF ls_venid-venid IS NOT INITIAL.
            CLEAR:lv_notes,ls_time,lt_time,lv_finishuser,ls_opera_per.

            "获取最后审批人信息
            lv_notes = lines( ls_detail-info-sp_record )."多少审批节点
*            lv_times = lines( ls_detail-info-sp_record[ lv_notes ]-details )."每个审批节点几个人依次审批
*            lv_finishuser = VALUE #( ls_detail-info-sp_record[ lv_notes ]-details[ lv_times ]-approver-userid OPTIONAL ).
            lt_time = ls_detail-info-sp_record[ lv_notes ]-details."每个审批节点几个人依次审批
            SORT lt_time BY sptime DESCENDING.
            READ TABLE lt_time INTO ls_time INDEX 1.
            lv_finishuser = ls_time-approver-userid.
            IF lv_finishuser IS NOT INITIAL.
              SELECT SINGLE userid,username INTO @ls_opera_per FROM ztint_user_inf WHERE qywxuserid = @lv_finishuser.
            ENDIF.

            UPDATE ztint_venclue_in SET accessstatus = '4' "未通过
                                        zupd_bdate = sy-datum
                                        zupd_btime = sy-uzeit
                                        zupd_uname = sy-uname
                                  WHERE venid = ls_venid-venid.

*****记录操作日志
            lv_content = |{ ls_opera_per-username }已拒绝商家线索({ ls_venid-venname })的准入申请|.
            TRY .
                ls_opera_log = VALUE ztint_opera_log( guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
                                                            fnam = 'M007' operatetion = 'O001' userid = ls_opera_per-userid
                                                            username = ls_opera_per-username objectid = ls_venid-venid
                                                            object = ls_venid-venname
                                                            content = lv_content operatedate = sy-datum operatetime = sy-uzeit ).
                MODIFY ztint_opera_log FROM ls_opera_log.
              CATCH cx_uuid_error.
            ENDTRY.
          ENDIF.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_access'
                    eventdesc = '商家准入申请' status = 'E' message = '更新商家准入申请拒绝状态未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ENDIF.

      WHEN 'terminate'."审批撤销
        CLEAR ls_ztint_venaccess.
        SELECT SINGLE * FROM ztint_venaccess INTO CORRESPONDING FIELDS OF ls_ztint_venaccess
          WHERE instanceid = iv_spno.
        IF sy-subrc = 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_access'
                    eventdesc = '商家准入申请' status = 'S' message = '更新商家准入申请状态撤销'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.

          "更新商家准入申请记录
          UPDATE ztint_venaccess SET status = 'CANCELED'
                                    msg =  '已撤销'
                                    zupd_bdate = sy-datum
                                    zupd_btime = sy-uzeit
                                    zupd_uname = sy-uname
                   WHERE guid = ls_ztint_venaccess-guid.

          "更新审批抬头表
          UPDATE ztint_dp_inf
            SET status = 'CANCELED'
                                    msg =  '已撤销'
                                    zupd_bdate = sy-datum
                                    zupd_btime = sy-uzeit
                                    zupd_uname = sy-uname
                   WHERE guid = ls_ztint_venaccess-guid.


          CLEAR:ls_venid,lv_content,ls_opera_log.
          SELECT SINGLE venid,venname INTO @ls_venid
            FROM ztint_venaccess
            WHERE venid = @ls_ztint_venaccess-venid.
          IF ls_venid-venid IS NOT INITIAL.
            UPDATE   ztint_venclue_in SET accessstatus = '4' "未通过
                                           zupd_bdate = sy-datum
                                           zupd_btime = sy-uzeit
                                           zupd_uname = sy-uname
                      WHERE venid = ls_ztint_venaccess-venid.
            CLEAR ls_opera_per.
            SELECT SINGLE i~userid,i~username INTO @ls_opera_per FROM ztint_user_inf AS i
              INNER JOIN ztint_dp_inf AS f ON i~userid = f~userid
             WHERE f~guid = @ls_ztint_venaccess-guid.
*****记录操作日志
            lv_content = |{ ls_opera_per-username }已撤销商家线索({ ls_venid-venname })的准入申请|.
            TRY .
                ls_opera_log = VALUE ztint_opera_log( guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
                                                            fnam = 'M007' operatetion = 'O001' userid = ls_opera_per-userid
                                                            username = ls_opera_per-username objectid = ls_venid-venid
                                                            object = ls_venid-venname
                                                            content = lv_content operatedate = sy-datum operatetime = sy-uzeit ).
                MODIFY ztint_opera_log FROM ls_opera_log.
              CATCH cx_uuid_error.
            ENDTRY.
          ENDIF.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_access'
                    eventdesc = '商家准入申请' status = 'E' message = '商家准入申请撤销未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ENDIF.
      WHEN OTHERS.
    ENDCASE.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPLY_VENDOR_OFFLINE
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD apply_vendor_offline.

    DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h,
          ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
          lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i,
          ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
          lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l,
          ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
    DATA:lt_user_content TYPE  zsint_push_userlist_tab,
         ls_user_content TYPE  zsint_push_userlist.
    DATA:lv_body          TYPE string,
         lv_date          TYPE string,
         lv_time          TYPE string,
         lv_instance      TYPE zde_intprocessinstanceid,
         lv_alert_content TYPE string,
         lv_fname         TYPE string,
         lv_url           TYPE string,
         lv_title         TYPE string,
         lv_log_msg       TYPE string,
         lv_msg_type      TYPE string,
         lv_content       TYPE string,
         lv_date_s        TYPE string.

    DATA:lv_interface TYPE c,
         lv_status    TYPE ztint_venoffline-status,
         lv_msg       TYPE ztint_venoffline-msg,
         lv_remark    TYPE ztint_venoffline-remark.

    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
    DATA(lv_code) = ls_detail-info-template_id.

    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
                           sendmsg = 'X' fname = 'BULELIST' ).
      RETURN.
    ENDIF.


*2 根据实例查找申请记录要存在
    SELECT SINGLE * FROM ztint_venoffline INTO @DATA(ls_ztint_venoffline) WHERE instanceid = @iv_spno.
    IF sy-subrc NE 0.
      ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_offline'
                eventdesc = '申请店铺下线' status = 'E' message = '未找到对应的申请记录'
                key1 = iv_spno key2 = lv_code ).
      MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      RETURN.
    ENDIF.
    "审批结果
    CASE iv_type.
      WHEN 'agree' OR 'refuse'. "审批通过
*6 检查流程实例是否已经处理过而且加锁要成功
        SELECT SINGLE * FROM ztint_dp_pi_h INTO CORRESPONDING FIELDS OF @ls_ztint_dp_pi_h WHERE processinstanceid = @iv_spno.
        IF ls_ztint_dp_pi_h-sendstatus = 'Y'. "处理过
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_offline'
                    eventdesc = '申请店铺下线' status = 'E' message = '实例店铺下线申请已处理过'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.
        lv_instance = iv_spno.
        "锁表
        CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.
        IF sy-subrc <> 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_offline'
                    eventdesc = '申请店铺下线' status = 'E' message = '实例店铺下线申请锁表失败'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.

        "抬头信息
        ls_ztint_dp_pi_h = prepare_ztint_dp_pi_h( is_detail = ls_detail iv_type = iv_type ).
        "审批详情
        LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
          CLEAR ls_ztint_dp_pi_i.
          ls_ztint_dp_pi_i = prepare_ztint_dp_pi_i( iv_spno = iv_spno is_content = ls_content ).
          APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.
        ENDLOOP.

        "获取最后审批人信息
        DATA(lv_notes) = lines( ls_detail-info-sp_record )."多少审批节点
        DATA(lt_time) = ls_detail-info-sp_record[ lv_notes ]-details."每个审批节点几个人依次审批
        sort lt_time by sptime DESCENDING.
        READ TABLE lt_time INTO DATA(ls_time) index 1.
        data(lv_finishuser) = ls_time-approver-userid.
        IF lv_finishuser IS NOT INITIAL.
          SELECT SINGLE userid,username INTO @DATA(ls_opera_per) FROM ztint_user_inf WHERE qywxuserid = @lv_finishuser.
        ENDIF.
*
        "获取店铺相关信息
        SELECT SINGLE venid,venname,displayname,productstoreid,companyid FROM ztint_ven_inf
          INTO @DATA(ls_veninf) WHERE venid = @ls_ztint_venoffline-venid.
*8 调用平台接口
        IF ls_ztint_venoffline-offtype EQ 'S'."店铺主动申请下线审批流
          CLEAR lv_body.
          lv_body = '{' &&
                   |"storeId":"{ ls_ztint_venoffline-productstoreid }","auditTime":"{ zcl_cassint_formatter=>convert_abap_to_timestamp( ) }",| &&
                   |"auditStatus":"{ COND #( WHEN iv_type EQ 'agree' THEN 'AUDIT_SUCCESS' WHEN iv_type EQ 'refuse' THEN 'AUDIT_FAIL') }",| &&
                   |{ COND #( WHEN iv_type EQ 'refuse' THEN |"auditReason":"{ zcl_cassint_formatter=>escape_for_json( EXACT string( lv_remark ) ) }",| ) }| &&
                   |"userLoginId":"{ ls_ztint_venoffline-userid }","userName":"{ ls_ztint_venoffline-username }"| && '}'.
          NEW zcl_icec_merchant_api( )->update_offline_audit(
            EXPORTING iv_body = lv_body IMPORTING es_return = DATA(ls_return) es_msg = DATA(ls_msg) ).
          IF ls_return-storeid IS NOT INITIAL.
            lv_interface = 'X'."更新成功
          ENDIF.
        ELSEIF ls_ztint_venoffline-offtype = 'L' ."强制下线XXXX
          IF iv_type EQ 'agree'."如果是强制下线被拒绝，不需要通知平台
            CLEAR lv_body.
            lv_body = '{' &&
                     |"companyId":"{ ls_veninf-companyid }","quitReason": "{ zcl_cassint_formatter=>escape_for_json( EXACT string( ls_ztint_venoffline-reason ) ) }",| &&
                     |"quitTime":"{ zcl_cassint_formatter=>convert_abap_to_timestamp( ) }","storeId": "{ ls_ztint_venoffline-productstoreid }",| &&
                     |"userLoginId": "{ ls_ztint_venoffline-userid }","userName": "{ ls_ztint_venoffline-username }",| &&
                     |"fileList": [ |.
            "文件
            SELECT num,filename,url INTO TABLE @DATA(lt_pic) FROM ztint_dp_file WHERE guid = @ls_ztint_venoffline-guid AND uploadsource = 'PICTURE'.
            IF sy-subrc EQ 0.
              LOOP AT lt_pic INTO DATA(ls_pic).
                lv_body = lv_body && '{ ' &&
                          |"fileName": "{ ls_pic-filename }","fileUrl":"{ ls_pic-url }","storeId":"{ ls_ztint_venoffline-productstoreid }"| &&
                          '}'.
                AT LAST.
                  lv_body =  lv_body && ']}'.
                  CONTINUE.
                ENDAT.
                lv_body = lv_body &&  ','.
              ENDLOOP.
            ENDIF.
            NEW zcl_icec_merchant_api( )->force_offline_quit(
             EXPORTING iv_body = lv_body IMPORTING es_return = DATA(ls_quit) es_msg = ls_msg ) .
            IF ls_quit-code EQ 'true'.
              lv_interface = 'X'."更新成功
            ENDIF.
          ELSE."如果是强制下线被拒绝，不需要通知平台
            lv_interface = 'X'.
          ENDIF.
        ENDIF.

        lv_status = COND #( WHEN iv_type EQ 'agree' THEN 'APPROVE' ELSE 'UNAPPROVE').
        lv_msg = COND #( WHEN iv_type EQ 'agree' THEN '成功' ELSE '').

        "更新店铺下线申请记录
        UPDATE ztint_venoffline SET status = lv_status "'APPROVE'
               remark = lv_remark msg =  lv_msg"'成功'
               zupd_bdate = sy-datum zupd_btime = sy-uzeit zupd_uname = sy-uname
         WHERE guid = ls_ztint_venoffline-guid.
        "更新审批抬头表
        UPDATE ztint_dp_inf SET status = lv_status "'APPROVE'
               remark = lv_remark msg =  lv_msg"'成功'
               zupd_bdate = sy-datum zupd_btime = sy-uzeit zupd_uname = sy-uname
         WHERE guid = ls_ztint_venoffline-guid.

        IF lv_interface EQ 'X'."表示处理成功
          "审批抬头表
          ls_ztint_dp_pi_h-sendstatus = 'Y'."已处理过
          ls_ztint_dp_pi_h-senddate = sy-datum.
          ls_ztint_dp_pi_h-sendtime = sy-uzeit.
          ls_ztint_dp_pi_h-sendmsg = '成功'.

          SELECT SINGLE * INTO @DATA(ls_offheader) FROM ztint_ven_offhea
            WHERE applyguid = @ls_ztint_venoffline-guid.
          IF sy-subrc EQ 0.
            "更新下线申请记录状态
            ls_offheader-offstatus = COND #( WHEN iv_type EQ 'agree' THEN '7' ELSE '5' )."'7'."待下线 '5'."不同意下线
            SELECT SINGLE offstatusdesc FROM ztint_ven_offsta INTO ls_offheader-offstatusdesc
                WHERE offstatus = ls_offheader-offstatus.
            ls_offheader-newest_offnode = COND #( WHEN iv_type EQ 'agree' THEN 'N0018' ELSE 'N0017')."'N0018'.同意下线 'N0017'."审核不通过
            ls_offheader-notallowmsg = lv_remark."审批意见备注
            ls_offheader-zupd_bdate = sy-datum.
            ls_offheader-zupd_btime = sy-uzeit.
            ls_offheader-zupd_uname = sy-uname.

*****更新处理流程
            DATA:ls_offflow TYPE ztint_ven_offflo,
                 lt_offflow TYPE STANDARD TABLE OF ztint_ven_offflo.
            CLEAR:ls_offflow,lt_offflow.
****获取flow表更新最新节点
            SELECT MAX( offflow ) AS offflow INTO @DATA(lv_maxflow) FROM ztint_ven_offflo WHERE offguid = @ls_offheader-offguid.
            ls_offflow-offguid = ls_offheader-offguid.
            ls_offflow-offflow = lv_maxflow + 1."流程序号
            TRY .
                ls_offflow-offnoteid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )."主键
              CATCH cx_uuid_error.
            ENDTRY.
            ls_offflow-offnode = COND #( WHEN iv_type EQ 'agree' THEN 'N0018' ELSE 'N0017')."'N0018'.同意下线 'N0017'."审核不通过
            SELECT SINGLE offnodedesc FROM ztint_ven_offnod INTO ls_offflow-offnodedesc WHERE offnode = ls_offflow-offnode."节点描述
            ls_offflow-zcrt_bdate = sy-datum.
            ls_offflow-zcrt_btime = sy-uzeit.
            ls_offflow-zcrt_uname = sy-uname.
            APPEND ls_offflow TO lt_offflow.
******新增处理节点描述
            DATA:ls_offnote TYPE ztint_ven_offno,
                 lt_offnote TYPE STANDARD TABLE OF ztint_ven_offno.
            CLEAR:ls_offnote,lt_offnote.
            ls_offnote-offnoteid = ls_offflow-offnoteid."节点GUID
            ls_offnote-offnoteseq = 1."序号
            ls_offnote-notemark = COND #( WHEN iv_type EQ 'agree' THEN |店铺下线审批已审核通过---{ ls_opera_per-username }|
                                               ELSE |店铺下线审批审核不通过---{ ls_opera_per-username }| ).
            ls_offnote-offguid = ls_offheader-offguid.
            ls_offnote-venid = ls_offheader-venid.
            ls_offnote-venname = ls_offheader-displayname.
            ls_offnote-notestatus = '2'."已完成
            ls_offnote-zcrt_bdate = sy-datum.
            ls_offnote-zcrt_btime = sy-uzeit.
            ls_offnote-zcrt_uname = sy-uname.
            APPEND ls_offnote TO lt_offnote.

            MODIFY ztint_ven_offhea FROM ls_offheader.
            MODIFY ztint_ven_offflo FROM TABLE lt_offflow.
            MODIFY ztint_ven_offno FROM TABLE lt_offnote.
*****准备钉钉消息通知抄送人
            SELECT userid INTO TABLE @DATA(lt_cc) FROM ztint_ven_offuse
              WHERE applyguid = @ls_ztint_venoffline-guid AND usertype = '3' AND isdelete = ''.
            IF sy-subrc EQ 0.
              CLEAR:lv_date,lv_time.
              lv_date = |{ sy-datum(4) }年{ sy-datum+4(2) }月{ sy-datum+6(2) }日|.
              lv_time = |{ sy-uzeit(2) }:{ sy-uzeit+2(2) }|.
              LOOP AT lt_cc INTO DATA(ls_cc).
                CLEAR ls_user_content.
                ls_user_content-userid = ls_cc-userid.
                ls_user_content-content = ' <font color=#FF8C00>开思助手</font> \n' && '##### **审批单【店铺下线申请】' &&
                                          |{ COND #( WHEN iv_type EQ 'agree' THEN '已审批通过' ELSE '审批不通过' ) }| && '，请知悉！** \n ' &&
                                          '店铺：' && ls_ztint_venoffline-displayname && '(' && ls_ztint_venoffline-productstoreid && ') ' .
                ls_user_content-content = ls_user_content-content &&  ' \n '  && |{ lv_date } { lv_time }|.
                ls_user_content-wxcontent = |<div>审批单【店铺下线申请】</div>| &&
                                            |<div class=\\"gray\\">{ lv_date } { lv_time }</div> | && | | &&
                                            |<div class=\\"highlight\\">{ COND #( WHEN iv_type EQ 'agree' THEN '已审批通过' ELSE '审批不通过' ) }，请知悉！| &&
                                            |<div>店铺：{ ls_ztint_venoffline-displayname }({ ls_ztint_venoffline-productstoreid  })</div>|.
                ls_user_content-orderno = |{ ls_ztint_venoffline-productstoreid }|.
                ls_user_content-orderdesc = |{ ls_ztint_venoffline-displayname }|.
                ls_user_content-firstcatgory = '店铺管理'.
                ls_user_content-secondcatgory = '店铺下线申请审批'.
                APPEND ls_user_content TO lt_user_content.
                CLEAR ls_cc.
              ENDLOOP.
            ENDIF.
*****记录操作日志
            lv_content = |{ ls_opera_per-username }已{ COND #( WHEN iv_type EQ 'agree' THEN '批准' ELSE '拒绝' ) }店铺({ ls_ztint_venoffline-displayname })的下线申请|.
            TRY .
                DATA(ls_opera_log) = VALUE ztint_opera_log( guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
                                                            fnam = 'M007' operatetion = 'O001' userid = ls_opera_per-userid
                                                            username = ls_opera_per-username objectid = ls_ztint_venoffline-venid
                                                            object = ls_ztint_venoffline-displayname
                                                            content = lv_content operatedate = sy-datum operatetime = sy-uzeit ).
                MODIFY ztint_opera_log FROM ls_opera_log.
              CATCH cx_uuid_error.
            ENDTRY.
          ENDIF.
        ELSE."接口处理失败
          "审批抬头表
          ls_ztint_dp_pi_h-sendstatus = 'N'."处理失败
          ls_ztint_dp_pi_h-senddate = sy-datum.
          ls_ztint_dp_pi_h-sendtime = sy-uzeit.
          "推送内部人员处理
          lv_alert_content = '开思助手 \n '  && ls_ztint_dp_pi_h-title  && |{ COND #( WHEN iv_type EQ 'agree' THEN '审批通过后' ELSE '审批拒绝后' ) }|
             && '调用电商接口失败,请关注! \n '
             && '实例号：' && ls_ztint_dp_pi_h-processinstanceid && ' \n '  && '流程code：' && ls_ztint_dp_pi_h-processcode && ' \n '
             && |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

          lv_fname = 'DPAPPLYVENDORONLINE'.
          lv_title =  '开思助手'.
          lv_msg_type = 'text'.

          CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
            EXPORTING
              iv_content = lv_alert_content
              iv_fname   = lv_fname.
          "接口调用失败通知发起人
          SELECT SINGLE userid FROM ztint_dp_inf INTO @DATA(lv_owner) WHERE guid = @ls_ztint_venoffline-guid.
          IF sy-subrc EQ 0.
            CLEAR ls_user_content.
            CLEAR:lv_date,lv_time.
            lv_date = |{ sy-datum(4) }年{ sy-datum+4(2) }月{ sy-datum+6(2) }日|.
            lv_time = |{ sy-uzeit(2) }:{ sy-uzeit+2(2) }|.
            ls_user_content-userid = ls_cc-userid.
            ls_user_content-content = ' <font color=#FF8C00>开思助手</font> \n' && '##### **审批单【店铺下线申请】' &&
                                      |{ COND #( WHEN iv_type EQ 'agree' THEN '审批通过后' ELSE '审批拒绝后' ) }| &&
                                      ',未能更新平台店铺状态,请联系开思助手系统管理员！** \n ' &&
                                      '店铺：' && ls_ztint_venoffline-displayname && '(' && ls_ztint_venoffline-productstoreid && ') ' .
            ls_user_content-content = ls_user_content-content &&  ' \n '  && |{ lv_date } { lv_time }|.
            ls_user_content-wxcontent = |<div>审批单【店铺下线申请】</div>| &&
                                        |<div class=\\"gray\\">{ lv_date } { lv_time }</div>| &&
                                        |<div> </div>| &&
                                        |<div class=\\"highlight\\">{ COND #( WHEN iv_type EQ 'agree' THEN '审批通过后' ELSE '审批拒绝后' ) }| &&
                                        |,未能更新平台店铺状态,请联系开思助手系统管理员！</div>| &&
                                        |<div>店铺：{ ls_ztint_venoffline-displayname }({ ls_ztint_venoffline-productstoreid  })</div>|.
            ls_user_content-orderno = |{ ls_ztint_venoffline-productstoreid }|.
            ls_user_content-orderdesc = |{ ls_ztint_venoffline-displayname }|.
            ls_user_content-firstcatgory = '店铺管理'.
            ls_user_content-secondcatgory = '店铺下线申请审批'.
            APPEND ls_user_content TO lt_user_content.
          ENDIF.
        ENDIF.

        MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.
        MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i.

        "解锁
        CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.
      WHEN 'terminate'."审批撤销
        "获取店铺相关信息
        SELECT SINGLE venid,venname,displayname,productstoreid,companyid FROM ztint_ven_inf
          INTO @ls_veninf WHERE venid = @ls_ztint_venoffline-venid.

        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_offline'
                  eventdesc = '申请店铺下线' status = 'S' message = '更新店铺下线申请状态撤销'
                  key1 = iv_spno key2 = lv_code ).
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.

        "更新商家准入申请记录
        UPDATE ztint_venoffline SET status = 'CANCELED' msg =  '已撤销'
               zupd_bdate = sy-datum zupd_btime = sy-uzeit zupd_uname = sy-uname
         WHERE guid = ls_ztint_venoffline-guid.

        "更新审批抬头表
        UPDATE ztint_dp_inf SET status = 'CANCELED' msg =  '已撤销'
               zupd_bdate = sy-datum zupd_btime = sy-uzeit zupd_uname = sy-uname
         WHERE guid = ls_ztint_venoffline-guid.

        CLEAR:ls_opera_per,lv_content,ls_offheader.
        SELECT SINGLE * INTO @ls_offheader FROM ztint_ven_offhea
          WHERE applyguid = @ls_ztint_venoffline-guid.
        IF sy-subrc EQ 0.
          SELECT SINGLE i~userid,i~username INTO @ls_opera_per FROM ztint_user_inf AS i
            INNER JOIN ztint_dp_inf AS f ON i~userid = f~userid
           WHERE f~guid = @ls_ztint_venoffline-guid.

          IF ls_offheader-offtype = 'S'."店铺申请
            ls_offheader-offstatus = '1'."待处理
            SELECT SINGLE offstatusdesc FROM ztint_ven_offsta INTO ls_offheader-offstatusdesc
                WHERE offstatus = ls_offheader-offstatus.
            ls_offheader-newest_offnode = 'N0019'."撤销了审批流
            ls_offheader-applyguid = ''.
            ls_offheader-zupd_bdate = sy-datum.
            ls_offheader-zupd_btime = sy-uzeit.
            ls_offheader-zupd_uname = sy-uname.
          ELSE."因为强制下线如果测试了审批流，则该笔记录需要关闭掉
            ls_offheader-offstatus = '9'."已关闭
            SELECT SINGLE offstatusdesc FROM ztint_ven_offsta INTO ls_offheader-offstatusdesc
                WHERE offstatus = ls_offheader-offstatus.
            ls_offheader-zupd_bdate = sy-datum.
            ls_offheader-zupd_btime = sy-uzeit.
            ls_offheader-zupd_uname = sy-uname.
          ENDIF.
*****更新处理流程
          CLEAR:ls_offflow,lv_maxflow,ls_offnote.
****获取flow表更新最新节点
          SELECT MAX( offflow ) AS offflow INTO @lv_maxflow FROM ztint_ven_offflo WHERE offguid = @ls_offheader-offguid.
          ls_offflow-offguid = ls_offheader-offguid.
          ls_offflow-offflow = lv_maxflow + 1."流程序号
          TRY .
              ls_offflow-offnoteid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )."主键
            CATCH cx_uuid_error.
          ENDTRY.
          ls_offflow-offnode = 'N0019'."撤销了审批流
          SELECT SINGLE offnodedesc FROM ztint_ven_offnod INTO ls_offflow-offnodedesc WHERE offnode = ls_offflow-offnode."节点描述
          ls_offflow-zcrt_bdate = sy-datum.
          ls_offflow-zcrt_btime = sy-uzeit.
          ls_offflow-zcrt_uname = sy-uname.
******新增处理节点描述
          ls_offnote-offnoteid = ls_offflow-offnoteid."节点GUID
          ls_offnote-offnoteseq = 1."序号
          ls_offnote-notemark = |{ ls_opera_per-username }撤销了店铺的下线申请|.
          ls_offnote-offguid = ls_offheader-offguid.
          ls_offnote-venid = ls_offheader-venid.
          ls_offnote-venname = ls_offheader-displayname.
          ls_offnote-notestatus = '2'."已完成
          ls_offnote-zcrt_bdate = sy-datum.
          ls_offnote-zcrt_btime = sy-uzeit.
          ls_offnote-zcrt_uname = sy-uname.

          MODIFY ztint_ven_offhea FROM ls_offheader.
          IF ls_offflow IS NOT INITIAL.
            MODIFY ztint_ven_offflo FROM ls_offflow.
            MODIFY ztint_ven_offno FROM ls_offnote.
          ENDIF.
*****准备钉钉消息通知抄送人
          CLEAR lt_cc.
          SELECT userid INTO TABLE @lt_cc FROM ztint_ven_offuse
            WHERE applyguid = @ls_ztint_venoffline-guid AND usertype = '3' AND isdelete = ''.
          IF sy-subrc EQ 0.
            CLEAR:lv_date,lv_time.
            lv_date = |{ sy-datum(4) }年{ sy-datum+4(2) }月{ sy-datum+6(2) }日|.
            lv_time = |{ sy-uzeit(2) }:{ sy-uzeit+2(2) }|.
            LOOP AT lt_cc INTO ls_cc.
              CLEAR ls_user_content.
              ls_user_content-userid = ls_cc-userid.
              ls_user_content-content = ' <font color=#FF8C00>开思助手</font> \n' && '##### **审批单【店铺下线申请】已撤销审批，请知悉！** \n ' &&
                                        '店铺：' && ls_ztint_venoffline-displayname && '(' && ls_ztint_venoffline-productstoreid && ') ' .
              ls_user_content-content = ls_user_content-content &&  ' \n '  && |{ lv_date } { lv_time }|.
              ls_user_content-wxcontent = |<div class=\\"highlight\\">审批单【店铺下线申请】已撤销审批，请知悉！</div>| &&
                                          |<div class=\\"gray\\">{ lv_date } { lv_time }</div>| &&
                                          | | &&
                                          |<div>店铺：{ ls_ztint_venoffline-displayname }({ ls_ztint_venoffline-productstoreid })</div>|.
              ls_user_content-orderno = |{ ls_ztint_venoffline-productstoreid }|.
              ls_user_content-orderdesc = |{ ls_ztint_venoffline-displayname }|.
              ls_user_content-firstcatgory = '店铺管理'.
              ls_user_content-secondcatgory = '店铺下线申请审批'.
              APPEND ls_user_content TO lt_user_content.
              CLEAR:ls_cc.
            ENDLOOP.
          ENDIF.

*****记录操作日志
          lv_content = |{ ls_opera_per-username }已撤销店铺({ ls_ztint_venoffline-displayname })的下线申请|.
          TRY .
              ls_opera_log = VALUE ztint_opera_log( guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
                                                          fnam = 'M007' operatetion = 'O001' userid = ls_opera_per-userid
                                                          username = ls_opera_per-username objectid = ls_ztint_venoffline-venid
                                                          object = ls_ztint_venoffline-displayname
                                                          content = lv_content operatedate = sy-datum operatetime = sy-uzeit ).
              MODIFY ztint_opera_log FROM ls_opera_log.
            CATCH cx_uuid_error.
          ENDTRY.
        ENDIF.
      WHEN OTHERS.
    ENDCASE.

    IF ( ls_ztint_venoffline-offtype = 'S' AND iv_type EQ 'refuse' ) OR"店铺主动下线拒绝时发出通知，回滚账单
       ( ls_ztint_venoffline-offtype = 'L' AND ( iv_type EQ 'refuse' OR iv_type EQ 'terminate' ) ).
      DATA:ls_notice TYPE zcl_cassint_venoffline=>ts_notice.
      ls_notice-companyid = ls_veninf-companyid.
      ls_notice-storeid = ls_veninf-productstoreid."店铺编码
      ls_notice-storename = ls_veninf-displayname.
      ls_notice-applytype = ls_ztint_venoffline-offtype.
      ls_notice-applystatus = 'UNAPPROVE'."拒绝
      ls_notice-timestamp = zcl_cassint_formatter=>convert_abap_to_timestamp(
        EXPORTING date = sy-datum time = sy-uzeit ).

      NEW zcl_cassint_venoffline( )->send_notice( is_data = ls_notice ).
    ENDIF.

    IF lt_user_content IS NOT INITIAL.
      lv_msg_type = 'textcard'."最终使用这个，配合URL
      lv_title =  '开思助手'.
      lv_log_msg = '店铺下线申请推送钉钉消息'.
      lv_url =  '#/supplierOffApply/detail/' && ls_ztint_venoffline-guid  && '?isMessagePush=true' .
      CALL FUNCTION 'Z_FMQYWX_CASSINT_PUSH_MESSAGE'
        IN UPDATE TASK
        EXPORTING
          iv_title    = lv_title
          iv_msgtype  = lv_msg_type
          iv_url      = lv_url
        TABLES
          it_userlist = lt_user_content.
    ENDIF.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPLY_VENDOR_ONLINE
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD apply_vendor_online.

    DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h,
          ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
          lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i,
          ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
          lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l,
          ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
    DATA:lt_user_content TYPE  zsint_push_userlist_tab,
         ls_user_content TYPE  zsint_push_userlist.
    DATA:lv_body          TYPE string,
         lv_instance      TYPE zde_intprocessinstanceid,
         lv_alert_content TYPE string,
         lv_fname         TYPE string,
         lv_url           TYPE string,
         lv_title         TYPE string,
         lv_msg_type      TYPE string,
         lv_content       TYPE string,
         lv_date_s        TYPE string.
    TYPES:BEGIN OF ty_brandid ,
            brandid TYPE char50,
          END OF ty_brandid.
    DATA:lt_brandid     TYPE STANDARD TABLE OF ty_brandid,
         ls_brandid_all TYPE  ty_brandid.

    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
    DATA(lv_code) = ls_detail-info-template_id.

    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
                           sendmsg = 'X' fname = 'BULELIST' ).
      RETURN.
    ENDIF.


    "审批结果
    CASE iv_type.
      WHEN 'agree'. "审批通过
        "查找申请记录
        SELECT SINGLE * FROM ztint_venonline INTO @DATA(ls_ztint_venonline)
          WHERE instanceid = @iv_spno.
        IF sy-subrc NE 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_online'
                    eventdesc = '商家上线申请' status = 'E' message = '未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.
        "判断是否已经处理过
        SELECT SINGLE * FROM ztint_dp_pi_h INTO CORRESPONDING FIELDS OF @ls_ztint_dp_pi_h
          WHERE processinstanceid = @iv_spno.
        IF ls_ztint_dp_pi_h-sendstatus = 'Y'. "处理过
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_online'
                    eventdesc = '商家上线申请' status = 'E' message = '实例商家上线申请已处理过'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.

        lv_instance = iv_spno.

        "锁表
        CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.
        IF sy-subrc <> 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_online'
                    eventdesc = '商家上线申请' status = 'E' message = '实例商家上线申请锁表失败'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.

        "抬头信息
        ls_ztint_dp_pi_h = prepare_ztint_dp_pi_h( is_detail = ls_detail iv_type = iv_type ).
        "审批详情
        LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
          CLEAR ls_ztint_dp_pi_i.
          ls_ztint_dp_pi_i = prepare_ztint_dp_pi_i( iv_spno = iv_spno is_content = ls_content ).
          APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.
        ENDLOOP.

        "调用平台接口通知生成签约协议
        DATA(lo_merchant) = NEW zcl_icec_merchant_api( ).
        DATA: lv_data TYPE string.
        DATA: lv_msg TYPE bapiret2.
        DATA: ls_signinfo       TYPE zmer_s_signinfo,
              lv_productstoreid TYPE string,
              lv_companyid      TYPE string,
              lv_belongto       TYPE ztint_ven_inf-belongto.

        "拼所需数据
        "取片联信息
        DATA(lo_ven) = NEW zcl_cassint_ven( ).

        DATA:lv_userid TYPE ztint_user_inf-userid.
        DATA:lv_deptid TYPE ztint_dept-deptid.
        DATA:lv_deptname TYPE ztint_dept-deptname.
        lv_userid = ls_ztint_venonline-userid.

**        CALL METHOD lo_ven->get_manager_org
**          EXPORTING
**            userid   = lv_userid
**          IMPORTING
**            deptid   = lv_deptid
**            deptname = lv_deptname.

        "店铺ID
        SELECT SINGLE productstoreid companyid belongto INTO ( lv_productstoreid,lv_companyid,lv_belongto )
          FROM ztint_ven_inf
          WHERE venid = ls_ztint_venonline-venid.
        "片联直接用所属组织
        SELECT SINGLE deptid deptname INTO ( lv_deptid,lv_deptname ) FROM ztint_dept WHERE deptid = lv_belongto.
        "申请人信息
        SELECT SINGLE * FROM ztint_user_inf INTO @DATA(ls_ztint_user_inf)
          WHERE userid = @lv_userid.

        lv_data = '{"storeId":"' && lv_productstoreid && '","storeName":"' && ls_ztint_venonline-displayname && '","pieceId":"' &&
        lv_deptid && '","pieceName":"' && lv_deptname && '","customerManagerId":"' &&  lv_userid && '","customerManager":"' && ls_ztint_user_inf-username
        && '","cellPhone":"' && ls_ztint_user_inf-telf && '"}'.

        "上线申请通过后调用平台接口
        CALL METHOD lo_merchant->add_merchant_signinfo
          EXPORTING
            iv_data     = lv_data
          IMPORTING
            ev_msg      = lv_msg
            es_signinfo = ls_signinfo.

        IF ls_signinfo-storeid IS NOT INITIAL."成功处理
          "审批抬头表
          ls_ztint_dp_pi_h-sendstatus = 'Y'."已处理过
          ls_ztint_dp_pi_h-sendmsg = '成功'.

          "更新商家准入申请记录
          UPDATE ztint_venonline SET status = 'APPROVE'
                                    msg =  '成功'
                                    zupd_bdate = sy-datum
                                    zupd_btime = sy-uzeit
                                    zupd_uname = sy-uname
                   WHERE guid = ls_ztint_venonline-guid.

          "更新审批抬头表
          UPDATE ztint_dp_inf SET  status = 'APPROVE'
                                   msg =  '成功'
                                   zupd_bdate = sy-datum
                                   zupd_btime = sy-uzeit
                                   zupd_uname = sy-uname
                  WHERE guid = ls_ztint_venonline-guid.

          "商家技术服务费收取方式
          DATA: ls_ztint_ven_pm TYPE ztint_ven_pm.
          ls_ztint_ven_pm-guid = ls_ztint_venonline-guid.
          ls_ztint_ven_pm-venid = ls_ztint_venonline-venid.
          ls_ztint_ven_pm-paymentmethod = ls_ztint_venonline-paymentmethod.
          ls_ztint_ven_pm-zcrt_bdate = sy-datum.
          ls_ztint_ven_pm-zcrt_btime = sy-uzeit.
          ls_ztint_ven_pm-zcrt_uname = sy-uname.
          MODIFY ztint_ven_pm FROM ls_ztint_ven_pm.

          SELECT SINGLE venid,venname INTO @DATA(ls_venid) FROM ztint_ven_inf WHERE venid = @ls_ztint_venonline-venid.
          IF sy-subrc EQ 0.
            "更新客户群
            UPDATE ztint_ven_inf SET vendortype = ls_ztint_venonline-ventype
                    WHERE venid = ls_ztint_venonline-venid.

            "获取最后审批人信息
            DATA(lv_notes) = lines( ls_detail-info-sp_record )."多少审批节点
*            DATA(lv_times) = lines( ls_detail-info-sp_record[ lv_notes ]-details )."每个审批节点几个人依次审批
*            DATA(lv_finishuser) = VALUE #( ls_detail-info-sp_record[ lv_notes ]-details[ lv_times ]-approver-userid OPTIONAL ).
            DATA(lt_time) = ls_detail-info-sp_record[ lv_notes ]-details."每个审批节点几个人依次审批
            SORT lt_time BY sptime DESCENDING.
            READ TABLE lt_time INTO DATA(ls_time) INDEX 1.
            DATA(lv_finishuser) = ls_time-approver-userid.
            IF lv_finishuser IS NOT INITIAL.
              SELECT SINGLE userid,username INTO @DATA(ls_opera_per) FROM ztint_user_inf WHERE qywxuserid = @lv_finishuser.
            ENDIF.

*****记录操作日志
            lv_content = |{ ls_opera_per-username }已批准商家({ ls_venid-venname })的上线申请|.
            TRY .
                DATA(ls_opera_log) = VALUE ztint_opera_log( guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
                                                            fnam = 'M007' operatetion = 'O001' userid = ls_opera_per-userid
                                                            username = ls_opera_per-username objectid = ls_venid-venid
                                                            object = ls_venid-venname
                                                            content = lv_content operatedate = sy-datum operatetime = sy-uzeit ).
                MODIFY ztint_opera_log FROM ls_opera_log.
              CATCH cx_uuid_error.
            ENDTRY.
          ENDIF.
          "add 是否旗舰店
          SELECT SINGLE venid INTO @DATA(ls_venext) FROM ztint_ven_exten WHERE venid = @ls_ztint_venonline-venid.
          IF sy-subrc EQ 0 .
            UPDATE ztint_ven_exten SET flagshipstore = ls_ztint_venonline-flagshipstore
            WHERE venid = ls_ztint_venonline-venid.
          ENDIF.

        ELSE.
          "调用接口失败
          "更新商家准入申请记录
          "审批抬头表
          ls_ztint_dp_pi_h-sendstatus = 'N'."处理失败
          ls_ztint_dp_pi_h-senddate = sy-datum.
          ls_ztint_dp_pi_h-sendtime = sy-uzeit.
          ls_ztint_dp_pi_h-sendmsg = lv_msg-message.
          UPDATE ztint_venonline SET status = 'APPROVE'
                                    msg =  lv_msg-message
                                    zupd_bdate = sy-datum
                                    zupd_btime = sy-uzeit
                                    zupd_uname = sy-uname
                   WHERE guid = ls_ztint_venonline-guid.

          "更新审批抬头表
          UPDATE ztint_dp_inf SET  status = 'APPROVE'
                                   msg =  lv_msg-message
                                   zupd_bdate = sy-datum
                                   zupd_btime = sy-uzeit
                                   zupd_uname = sy-uname
                  WHERE guid = ls_ztint_venonline-guid.

          "推送内部人员处理
          CLEAR lv_date_s.
          CLEAR lv_content.
          lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

          lv_alert_content = '开思助手 \n '  && ls_ztint_dp_pi_h-title  && '审批后调用电商接口失败,请关注! \n '
             && '实例号：' && ls_ztint_dp_pi_h-processinstanceid && ' \n '
             && '流程code：' && ls_ztint_dp_pi_h-processcode && ' \n '
             && lv_date_s.

          lv_fname = 'DPAPPLYVENDORONLINE'.
          lv_title =  '开思助手'.
          lv_msg_type = 'text'.

          CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
            EXPORTING
              iv_content = lv_alert_content
              iv_fname   = lv_fname.
        ENDIF.

        "上线审批通过后同步'客户群'商家类型，是否集团报价范围数据给会员
        SELECT SINGLE username INTO @DATA(lv_username) FROM ztint_user_inf WHERE userid = @lv_userid.
        IF ls_ztint_venonline-ventype IS NOT INITIAL OR ls_ztint_venonline-zones IS NOT INITIAL OR ls_ztint_venonline-isgroupven IS NOT INITIAL.
          DATA:lv_zonetype_a TYPE string.
          lv_zonetype_a = lo_merchant->get_quote_zone( EXPORTING iv_guid = EXACT string( ls_ztint_venonline-guid ) ).
          DATA(lv_zonetype) = COND #( WHEN lv_zonetype_a EQ '区域供应商' THEN '1000006'
                                      WHEN lv_zonetype_a EQ '末端供应商' THEN '1000007'
                                       WHEN lv_zonetype_a EQ '全国供应商' THEN '1000008').
          DATA(lv_customers) = COND #( WHEN ls_ztint_venonline-ventype EQ 'N' THEN '1000005' "普通供应商
                                       WHEN ls_ztint_venonline-ventype EQ 'S' THEN '1000004')."专属供应商
          DATA(lv_isgroupven) = COND #( WHEN ls_ztint_venonline-isgroupven EQ 'Y' THEN '1000072' ELSE '1000073' ).
          DATA(lv_tagids) = |[ "{ lv_zonetype }","{ lv_customers }","{ lv_isgroupven }"]|.
          CLEAR lv_body.
          lv_body = '{' && |"companyId":"{ lv_companyid }","operator":"{ lv_username }" ,"tagIds":{ lv_tagids }| && '}'.
          CALL METHOD lo_merchant->add_merchant_companytags
            EXPORTING
              iv_body = lv_body
            IMPORTING
              ev_msg  = lv_msg
              es_info = DATA(ls_info).
          UPDATE ztint_ven_inf SET isgroupven = ls_ztint_venonline-isgroupven
                 WHERE venid = ls_ztint_venonline-venid.
        ENDIF.
        "上线审批通过后同步汽车品牌数据给会员
        IF ls_ztint_venonline-brandid IS NOT INITIAL.
          DATA:lv_carbrands TYPE string.
          SPLIT ls_ztint_venonline-brandid AT ',' INTO TABLE lt_brandid.
          READ TABLE lt_brandid INTO ls_brandid_all WITH KEY brandid = 'A' .
          IF sy-subrc EQ 0.
            REFRESH lt_brandid.
            SELECT carbrandid AS brandid INTO TABLE  lt_brandid FROM ztint_carbrand WHERE carbrandid NE 'A'.
          ENDIF.
          SORT lt_brandid BY brandid.
          DELETE ADJACENT DUPLICATES FROM lt_brandid.

          LOOP AT lt_brandid INTO DATA(ls_brandid).
            IF lv_carbrands IS INITIAL.
              lv_carbrands = |"{ ls_brandid-brandid }"|.
            ELSE.
              lv_carbrands = |{ lv_carbrands },"{ ls_brandid-brandid }"|.
            ENDIF.
          ENDLOOP.
          lv_carbrands = |[{ lv_carbrands }]|.
          CLEAR lv_body.
          lv_body = '{' && |"companyId":"{ lv_companyid }","carBrands":{ lv_carbrands },"operator":"{ lv_username }"| && '}'.
          CALL METHOD lo_merchant->add_merchant_vencarinfo
            EXPORTING
              iv_body    = lv_body
            IMPORTING
              ev_msg     = lv_msg
              es_carinfo = DATA(ls_carinfo).
        ENDIF.

        "上线通过后同步主营品类标签至会员
        IF ls_ztint_venonline-mainclass IS NOT INITIAL.
          IF ls_ztint_venonline-mainclassdesc IS INITIAL.
            SELECT SINGLE text FROM ztbc_f4_config INTO ls_ztint_venonline-mainclassdesc
              WHERE fnam = 'ZSINT_VEN_MAINCLASS' AND val_low = ls_ztint_venonline-mainclass.
          ENDIF.
          CLEAR lv_body.
          lv_body = |\{"companyId":"{ lv_companyid }","operator":"{ lv_username }",| &&
                    |"tags":[\{"code":"{ ls_ztint_venonline-mainclass }","name":"{ ls_ztint_venonline-mainclassdesc }"\}]\}|.
          DATA(lo_user) = NEW zcl_icec_user_api( ).
          CALL METHOD lo_user->add_companytags_bycompanyid
            EXPORTING
              iv_data = lv_body.

          UPDATE ztint_ven_exten SET mainclass  = ls_ztint_venonline-mainclass WHERE venid = ls_ztint_venonline-venid.

        ENDIF.


        MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.
        MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i.

        "解锁
        CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.
      WHEN 'refuse'."审批拒绝
        CLEAR ls_ztint_venonline.
        SELECT SINGLE * FROM ztint_venonline INTO CORRESPONDING FIELDS OF ls_ztint_venonline
          WHERE instanceid = iv_spno.

        IF sy-subrc = 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_online'
                    eventdesc = '商家上线申请' status = 'S' message = '更新商家上线申请拒绝状态'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          "更新商家准入申请记录
          UPDATE ztint_venonline SET status = 'UNAPPROVE'
                                    msg =  '审批已被拒绝'
                                    zupd_bdate = sy-datum
                                    zupd_btime = sy-uzeit
                                    zupd_uname = sy-uname
                   WHERE guid = ls_ztint_venonline-guid.

          "更新审批抬头表
          UPDATE ztint_dp_inf SET status = 'UNAPPROVE'
                                    msg =  '审批已被拒绝'
                                    zupd_bdate = sy-datum
                                    zupd_btime = sy-uzeit
                                    zupd_uname = sy-uname
                   WHERE guid = ls_ztint_venonline-guid.

          CLEAR:ls_venid,ls_opera_log,lt_time,ls_time,lv_content.
          SELECT SINGLE venid,venname INTO @ls_venid FROM ztint_ven_inf WHERE venid = @ls_ztint_venonline-venid.
          IF sy-subrc EQ 0.
            "获取最后审批人信息
            lv_notes = lines( ls_detail-info-sp_record )."多少审批节点
            lt_time = ls_detail-info-sp_record[ lv_notes ]-details."每个审批节点几个人依次审批
            SORT lt_time BY sptime DESCENDING.
            READ TABLE lt_time INTO ls_time INDEX 1.
            lv_finishuser = ls_time-approver-userid.
            IF lv_finishuser IS NOT INITIAL.
              SELECT SINGLE userid,username INTO @ls_opera_per FROM ztint_user_inf WHERE qywxuserid = @lv_finishuser.
            ENDIF.
*****记录操作日志
            lv_content = |{ ls_opera_per-username }已拒绝商家({ ls_venid-venname })的上线申请|.
            TRY .
                ls_opera_log = VALUE ztint_opera_log( guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
                                                            fnam = 'M007' operatetion = 'O001' userid = ls_opera_per-userid
                                                            username = ls_opera_per-username objectid = ls_venid-venid
                                                            object = ls_venid-venname
                                                            content = lv_content operatedate = sy-datum operatetime = sy-uzeit ).
                MODIFY ztint_opera_log FROM ls_opera_log.
              CATCH cx_uuid_error.
            ENDTRY.
          ENDIF.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_online'
                    eventdesc = '商家上线申请' status = 'E' message = '更新商家上线申请拒绝状态未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ENDIF.
      WHEN 'terminate'."审批撤销
        CLEAR ls_ztint_venonline.
        SELECT SINGLE * FROM ztint_venonline INTO CORRESPONDING FIELDS OF ls_ztint_venonline
          WHERE instanceid = iv_spno.
        IF sy-subrc = 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_online'
                    eventdesc = '商家上线申请' status = 'S' message = '更新商家上线申请状态撤销'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.

          "更新商家准入申请记录
          UPDATE ztint_venonline SET status = 'CANCELED'
                                    msg =  '已撤销'
                                    zupd_bdate = sy-datum
                                    zupd_btime = sy-uzeit
                                    zupd_uname = sy-uname
                   WHERE guid = ls_ztint_venonline-guid.

          "更新审批抬头表
          UPDATE ztint_dp_inf SET status = 'CANCELED'
                                    msg =  '已撤销'
                                    zupd_bdate = sy-datum
                                    zupd_btime = sy-uzeit
                                    zupd_uname = sy-uname
                   WHERE guid = ls_ztint_venonline-guid.

          CLEAR:ls_venid,ls_opera_log,lv_content.
          SELECT SINGLE venid,venname INTO @ls_venid FROM ztint_ven_inf WHERE venid = @ls_ztint_venonline-venid.
          IF sy-subrc EQ 0.
            CLEAR ls_opera_per.
            SELECT SINGLE i~userid,i~username INTO @ls_opera_per FROM ztint_user_inf AS i
              INNER JOIN ztint_dp_inf AS f ON i~userid = f~userid
             WHERE f~guid = @ls_ztint_venonline-guid.
*****记录操作日志
            lv_content = |{ ls_opera_per-username }已撤销商家({ ls_venid-venname })的上线申请|.
            TRY .
                ls_opera_log = VALUE ztint_opera_log( guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
                                                            fnam = 'M007' operatetion = 'O001' userid = ls_opera_per-userid
                                                            username = ls_opera_per-username objectid = ls_venid-venid
                                                            object = ls_venid-venname
                                                            content = lv_content operatedate = sy-datum operatetime = sy-uzeit ).
                MODIFY ztint_opera_log FROM ls_opera_log.
              CATCH cx_uuid_error.
            ENDTRY.
          ENDIF.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_online'
                    eventdesc = '商家上线申请' status = 'E' message = '商家上线申请撤销未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.

        ENDIF.
      WHEN OTHERS.
    ENDCASE.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPLY_VENDOR_QUALIFY
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
METHOD apply_vendor_qualify.

  DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h,
        ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
        lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i,
        ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
        lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l,
        ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
  DATA:lt_user_content TYPE  zsint_push_userlist_tab,
       ls_user_content TYPE  zsint_push_userlist.
  DATA:lv_body          TYPE string,
       lv_date          TYPE string,
       lv_time          TYPE string,
       lv_instance      TYPE zde_intprocessinstanceid,
       lv_alert_content TYPE string,
       lv_fname         TYPE string,
       lv_url           TYPE string,
       lv_title         TYPE string,
       lv_log_msg       TYPE string,
       lv_msg_type      TYPE string,
       lv_content       TYPE string,
       lv_date_s        TYPE string.

  DATA:lv_interface TYPE c,
       lv_status    TYPE ztint_venqualify-status,
       lv_msg       TYPE ztint_venqualify-msg,
       lv_remark    TYPE ztint_venqualify-remark.
  DATA :lt_recordlist TYPE  zsint_push_recordlist_tab.
  DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
  DATA(lv_code) = ls_detail-info-template_id.
  DATA ls_ztint_venquain TYPE ztint_venquain.
  DATA:lv_bapi_msg TYPE bapi_msg.
  DATA ls_ztint_log TYPE ztint_wo_log.
  DEFINE get_intlog.
    ls_ztint_log-guid = ls_ztint_venquain-guid.
    ls_ztint_log-status = &1.
    ls_ztint_log-event_id = 'PROFIUPDATE'.
    ls_ztint_log-event_desc = &2.
    ls_ztint_log-key_value1 = &3.
    ls_ztint_log-MESSAGE = &4.
    ls_ztint_log-zcrt_bdate = sy-datum.
    ls_ztint_log-zcrt_btime = sy-uzeit.
    MODIFY ztint_wo_log FROM  ls_ztint_log.
    CLEAR ls_ztint_log.
  END-OF-DEFINITION.

*2 根据实例查找申请记录要存在
  SELECT SINGLE * FROM ztint_venquain INTO @ls_ztint_venquain WHERE instanceid = @iv_spno.
  IF sy-subrc NE 0.
    ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_qualify'
    eventdesc = '申请资质认证' status = 'E' message = '未找到对应的申请记录'
    key1 = iv_spno key2 = lv_code ).
    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
*      lv_bapi_msg = '未找到对应的申请记录！'.
*      RAISE EXCEPTION TYPE /iwbep/cx_mgw_busi_exception
*        EXPORTING
*          textid  = /iwbep/cx_mgw_busi_exception=>business_error
*          message = lv_bapi_msg.
    RETURN.
  ENDIF.
  "先更新钉钉状态证明单据被审批过，一级审批同意时要根据dingstatus 判定是否有对应待审批的单
  UPDATE ztint_venquain SET dingstatus = iv_type
  zupd_bdate = sy-datum
  zupd_btime = sy-uzeit
  zupd_uname = sy-uname
  WHERE guid = ls_ztint_venquain-guid.

  IF ls_detail-errcode NE '0'.
    "没找到 记录日志
    log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
    zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
    sendmsg = 'X' fname = 'BULELIST' ).
    RETURN.
  ENDIF.

*根据公司ID获取资质认证详情
  DATA(lo_agreement) = NEW zcl_icec_agreement_api( ).
  lo_agreement->get_companyprofile_bycompanyid(
  EXPORTING iv_companyid = EXACT string( ls_ztint_venquain-companyid )
  IMPORTING ev_msg = DATA(lv_msgpl) es_companydprofile = DATA(ls_companyprofiles) ).
*    IF lv_msgpl-type EQ 'E'.
*      lv_bapi_msg = '调用接口获取资质认证详情失败！'.
*      RAISE EXCEPTION TYPE /iwbep/cx_mgw_busi_exception
*        EXPORTING
*          textid  = /iwbep/cx_mgw_busi_exception=>business_error
*          message = lv_bapi_msg.
*    ENDIF.
  IF ls_companyprofiles-authenticationstatus = 'APPROVE' OR ls_companyprofiles-authenticationstatus = 'UNAPPROVE'.
    UPDATE ztint_profile SET approvestatus = ls_companyprofiles-authenticationstatus WHERE companyid = ls_ztint_venquain-companyid.
    CASE ls_companyprofiles-authenticationstatus.
      WHEN 'APPROVE'.
        lv_bapi_msg = '平台已将审批状态变更为：同意，请检查！'.
      WHEN 'UNAPPROVE'.
        lv_bapi_msg = '平台已将审批状态变更为：不同意，请检查！'.
    ENDCASE.
    get_intlog 'E' '资质审核提交' ls_ztint_venquain-zcrt_uname lv_bapi_msg .
    ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_qualify'
    eventdesc = '申请资质认证' status = 'E' message = 'OA审批前平台已变更状态为同意/不同意,请检查！'
    key1 = iv_spno key2 = lv_code ).
    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
    RETURN.
*      RAISE EXCEPTION TYPE /iwbep/cx_mgw_busi_exception
*        EXPORTING
*          textid  = /iwbep/cx_mgw_busi_exception=>business_error
*          message = lv_bapi_msg.
  ENDIF.
  "审批结果
  CASE iv_type.
    WHEN 'agree'. "审批通过
*6 检查流程实例是否已经处理过而且加锁要成功
      SELECT SINGLE * FROM ztint_dp_pi_h INTO CORRESPONDING FIELDS OF @ls_ztint_dp_pi_h WHERE processinstanceid = @iv_spno.
      IF ls_ztint_dp_pi_h-sendstatus = 'Y'. "处理过
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_qualify'
        eventdesc = '申请资质认证' status = 'E' message = '资质认证申请已处理过'
        key1 = iv_spno key2 = lv_code ).
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        RETURN.
      ENDIF.
      lv_instance = iv_spno.
      "锁表
      CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
        EXPORTING
          mode_ztint_dp_pi_h = 'E'
          mandt              = sy-mandt
          instanceid         = lv_instance.
      IF sy-subrc <> 0.
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_qualify'
        eventdesc = '申请资质认证' status = 'E' message = '实例资质认证申请锁表失败'
        key1 = iv_spno key2 = lv_code ).
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        RETURN.
      ENDIF.

      "抬头信息
      ls_ztint_dp_pi_h = prepare_ztint_dp_pi_h( is_detail = ls_detail iv_type = iv_type ).
      "审批详情
      LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
        CLEAR ls_ztint_dp_pi_i.
        ls_ztint_dp_pi_i = prepare_ztint_dp_pi_i( iv_spno = iv_spno is_content = ls_content ).
        APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.
      ENDLOOP.

      "获取最后审批人信息
      DATA(lv_notes) = lines( ls_detail-info-sp_record )."多少审批节点
      DATA(lt_time) = ls_detail-info-sp_record[ lv_notes ]-details."每个审批节点几个人依次审批
      SORT lt_time BY sptime DESCENDING.
      READ TABLE lt_time INTO DATA(ls_time) INDEX 1.
      DATA(lv_finishuser) = ls_time-approver-userid.
      IF lv_finishuser IS NOT INITIAL.
        SELECT SINGLE userid,username INTO @DATA(ls_opera_per) FROM ztint_user_inf WHERE qywxuserid = @lv_finishuser.
      ENDIF.
*
      "获取商家相关信息
*        SELECT SINGLE * FROM ztint_venqualify
*          INTO @DATA(ls_venqualify) WHERE companyid = @ls_ztint_venquain-companyid.
      SELECT SINGLE * FROM ztint_venqualify
      INTO @DATA(ls_venqualify) WHERE instanceid = @iv_spno.
*8 调用平台接口
      "资质认证审批流
      CLEAR lv_body.

      lv_body = '{' &&
      |"companyId": "{ ls_venqualify-companyid }","companyName": "{ ls_venqualify-companyname }",| &&
      |"companyType": "{ ls_venqualify-companytype }","businessLicenseName": "{ ls_venqualify-businesslicensename }",| &&
      |"businessAddress": "{ ls_venqualify-businessaddress }","legalPerson": "{ ls_venqualify-legalperson }",| &&
      |"contactPerson": "{ ls_venqualify-contactperson }","contactNumber": "{ ls_venqualify-contactnumber }",| &&
      |"regCode": "{ ls_venqualify-regcode }","businessLicenseCertificate": "{ ls_venqualify-businesslicensecertificate }",| &&
      |"shopFacade": "{ ls_venqualify-shopfacade }","workplace1": "{ ls_venqualify-workplace1 }",| &&
      | "workplace2": "{ ls_venqualify-workplace2 }","auditRemark": "{ ls_venqualify-remark }",| &&
      |"status": "{ ls_venqualify-status }","operator": "{ ls_venqualify-zcrt_uname }"| && '}'.

      lo_agreement->update_company_companyprofile( EXPORTING iv_body = lv_body
      IMPORTING ev_msg = DATA(ev_msg) ov_status = DATA(ov_status) ).

*        lv_status = COND #( WHEN iv_type EQ 'agree' THEN 'APPROVE' ELSE 'UNAPPROVE').
*        lv_msg = COND #( WHEN iv_type EQ 'agree' THEN '成功' ELSE '').
      lv_status = ov_status.
      lv_msg = ev_msg-message.
      lv_remark = ls_venqualify-remark.

      "移除客户公海标志
      SELECT SINGLE * INTO @DATA(ls_ztint_cus_inf)
            FROM ztint_cus_inf
            WHERE companyid = @ls_venqualify-companyid.
      IF sy-subrc = 0.

        IF ls_ztint_cus_inf-ispublic = 'X'.
          CLEAR ls_ztint_cus_inf-ispublic ."如果是公海客户 则变成非公海客户
          ls_ztint_cus_inf-zupd_bdate = sy-datum.
          ls_ztint_cus_inf-zupd_btime = sy-uzeit.
          ls_ztint_cus_inf-zupd_uname = sy-uname.
          MODIFY ztint_cus_inf FROM ls_ztint_cus_inf.
        ENDIF.

        "判定有没有客户经理，没有的话，谁审批客户经理就给谁(不管平台返回状态是E还是S（lv_msg-type = E的同步了对应客户经理也没影响）)
        "因为当lv_msg-type = E， lv_bapi_msg = 平台已将审批状态变更为:同意，请检查!时 如果没有客户经理也要同步）
        DATA ls_cus_user TYPE ztint_cus_user.
        CLEAR ls_cus_user.
        SELECT SINGLE *
        INTO @ls_cus_user
        FROM ztint_cus_user
        WHERE cusid = @ls_ztint_cus_inf-cusid
        AND usertype = '1' AND ispre = '' AND isdelete = ''.
        IF sy-subrc <> 0."没找到客户经理
          "分配客户经理
          SELECT SINGLE cusid INTO @ls_cus_user-cusid FROM ztint_cus_inf WHERE companyid = @ls_venqualify-companyid.
          "审批人获取
          "审批人获取
          SELECT SINGLE * INTO @DATA(ls_prouser)
                FROM ztint_pro_user
                WHERE companyid = @ls_venqualify-companyid
                AND usertype = 'REGISTE_RPROFILE'
                AND isleader = ''
                AND isdelete = ''.
          ls_cus_user-usertype = '1'.
          ls_cus_user-userid = ls_prouser-userid.
          ls_cus_user-zcrt_bdate = sy-datum.
          ls_cus_user-zcrt_btime = sy-uzeit.
          ls_cus_user-zupd_bdate = sy-datum.
          ls_cus_user-zupd_btime = sy-uzeit.
          MODIFY ztint_cus_user FROM ls_cus_user.
          "系统同步客户经理的要存操作日志

          TRY.
              lv_content = | #客户后台分配#{ ls_opera_per-username }将客户分配给{ ls_prouser-username } |.
              DATA(ls_operalog) = VALUE ztint_opera_log( guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
                    userid = ls_opera_per-userid username = ls_opera_per-username
                    operatedate = sy-datum operatetime = sy-uzeit fnam = ' M001'  operatetion = 'O006'
                    objectid = ls_cus_user-cusid object = ''
                    content = lv_content
                    ).
              MODIFY ztint_opera_log FROM ls_operalog.
            CATCH cx_uuid_error INTO DATA(lo_error).
          ENDTRY.
        ENDIF.
      ELSE. "对应companyid在ztint_cus_inf 没找到记个日志
        get_intlog 'E' '资质认证客户经理获取' ls_venqualify-companyid '客户获取失败' .
      ENDIF.
      CLEAR lv_interface.
      IF ev_msg-type NE 'E'.
*          er_deep_entity-statusdesc = 'succeed'.
        get_intlog 'S' '资质审核提交' ls_ztint_venquain-zcrt_uname '提交成功' .
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_qualify'
        eventdesc = '申请资质认证' status = 'S' message = '资质认证OA申请提交平台成功'
        key1 = iv_spno key2 = lv_code ).
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        lv_interface = 'X'.
      ELSE.
*          er_deep_entity-statusdesc = 'failed'.
        CLEAR:lv_bapi_msg.
        lv_bapi_msg = '提交失败'.
        get_intlog 'E' '资质审核提交' ls_ztint_venquain-zcrt_uname lv_bapi_msg .
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_qualify'
        eventdesc = '申请资质认证' status = 'E' message = '资质认证OA申请提交平台失败'
        key1 = iv_spno key2 = lv_code ).
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
*          RAISE EXCEPTION TYPE /iwbep/cx_mgw_busi_exception
*            EXPORTING
*              textid  = /iwbep/cx_mgw_busi_exception=>business_error
*              message = lv_bapi_msg.
      ENDIF.

      IF ev_msg-type NE 'E'.

        SELECT SINGLE * FROM ztint_profile INTO @DATA(ls_ztint_profile)
              WHERE  companyid = @ls_venqualify-companyid.
        IF sy-subrc = 0.
          ls_ztint_profile-companyid = ls_venqualify-companyid.
          ls_ztint_profile-companyname =  ls_venqualify-companyname.
          ls_ztint_profile-approvestatus  = ls_venqualify-status.
          ls_ztint_profile-remark = ls_venqualify-remark .
          ls_ztint_profile-operator = ls_venqualify-zupd_uname.
          ls_ztint_profile-approvedate = sy-datum.
          ls_ztint_profile-approvetime = sy-uzeit.
          MODIFY ztint_profile FROM  ls_ztint_profile.
        ENDIF.

* 取消原待办推送
        SELECT * FROM ztint_wrecord_h INTO TABLE @DATA(lt_ztint_wrecord_h)
              WHERE fnam = 'INTWOPROFILESUBMITTED'
              AND   value =  @ls_ztint_profile-companyid .

        MOVE-CORRESPONDING lt_ztint_wrecord_h TO lt_recordlist.
      ENDIF.

      IF lt_recordlist IS NOT  INITIAL .
        lv_log_msg = '资质认证待办更新'.
        CALL FUNCTION 'Z_FMINT_WO_PUSH_WORKRECORD'
          IN UPDATE TASK
          EXPORTING
            iv_log_msg    = lv_log_msg
            iv_update     = 'X'
          TABLES
            it_recordlist = lt_recordlist.
      ENDIF.


*    ENDIF.

      COMMIT WORK .
      "更新资质认证申请记录
      UPDATE ztint_venquain SET dingstatus = 'agree' "'APPROVE'
      remark = lv_remark msg =  lv_msg"'成功'
      zupd_bdate = sy-datum zupd_btime = sy-uzeit zupd_uname = sy-uname
      WHERE guid = ls_ztint_venquain-guid.
      "更新审批抬头表
      UPDATE ztint_dp_inf SET status = ls_venqualify-status "'APPROVE'
      remark = lv_remark msg =  'agree'"'成功'
      zupd_bdate = sy-datum zupd_btime = sy-uzeit zupd_uname = sy-uname
      WHERE guid = ls_ztint_venquain-guid.

      IF lv_interface EQ 'X'."表示处理成功
        "审批抬头表
        ls_ztint_dp_pi_h-sendstatus = 'Y'."已处理过
        ls_ztint_dp_pi_h-senddate = sy-datum.
        ls_ztint_dp_pi_h-sendtime = sy-uzeit.
        ls_ztint_dp_pi_h-sendmsg = '成功'.

      ELSE."接口处理失败
        "审批抬头表
        ls_ztint_dp_pi_h-sendstatus = 'N'."处理失败
        ls_ztint_dp_pi_h-senddate = sy-datum.
        ls_ztint_dp_pi_h-sendtime = sy-uzeit.

      ENDIF.

      MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.
      MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i.

      "解锁
      CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
        EXPORTING
          mode_ztint_dp_pi_h = 'E'
          mandt              = sy-mandt
          instanceid         = lv_instance.
    WHEN 'refuse'."审批拒绝
      CLEAR ls_ztint_venquain.
      SELECT SINGLE * FROM ztint_venquain INTO CORRESPONDING FIELDS OF ls_ztint_venquain
      WHERE instanceid = iv_spno.

      IF sy-subrc = 0.
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_qualify'
        eventdesc = '申请资质认证' status = 'S' message = '申请资质认证拒绝状态'
        key1 = iv_spno key2 = lv_code ).
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        "更新商家准入申请记录
        UPDATE ztint_venquain SET dingstatus = 'refuse'
        msg =  '审批已被拒绝'
        zupd_bdate = sy-datum
        zupd_btime = sy-uzeit
        zupd_uname = sy-uname
        WHERE guid = ls_ztint_venquain-guid.

        "更新审批抬头表
        UPDATE ztint_dp_inf SET   status = ls_venqualify-status
        msg =  'refuse'
        zupd_bdate = sy-datum
        zupd_btime = sy-uzeit
        zupd_uname = sy-uname
        WHERE guid = ls_ztint_venquain-guid.


      ELSE.
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_qualify'
        eventdesc = '申请资质认证' status = 'E' message = '申请资质认证拒绝状态未找到对应的申请记录'
        key1 = iv_spno key2 = lv_code ).
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      ENDIF.
    WHEN 'terminate'."审批撤销
      CLEAR ls_ztint_venquain.
      SELECT SINGLE * FROM ztint_venquain INTO CORRESPONDING FIELDS OF ls_ztint_venquain
      WHERE instanceid = iv_spno.
      IF sy-subrc = 0.
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_qualify'
        eventdesc = '申请资质认证' status = 'S' message = '申请资质认证状态撤销'
        key1 = iv_spno key2 = lv_code ).
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.

        "更新商家准入申请记录
        UPDATE ztint_venquain SET dingstatus = 'terminate'
        msg =  '已撤销'
        zupd_bdate = sy-datum
        zupd_btime = sy-uzeit
        zupd_uname = sy-uname
        WHERE guid = ls_ztint_venquain-guid.

        "更新审批抬头表
        UPDATE ztint_dp_inf SET status = ls_venqualify-status
        msg =  'terminate'
        zupd_bdate = sy-datum
        zupd_btime = sy-uzeit
        zupd_uname = sy-uname
        WHERE guid = ls_ztint_venquain-guid.


      ELSE.
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_qualify'
        eventdesc = '申请资质认证' status = 'E' message = '申请资质认证撤销未找到对应的申请记录'
        key1 = iv_spno key2 = lv_code ).
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.

      ENDIF.
    WHEN OTHERS.
  ENDCASE.

ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPLY_VENDOR_SPECIALPOLICY
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD apply_vendor_specialpolicy.

    DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h,
          ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
          lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i,
          ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
          lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l,
          ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
    DATA:lt_user_content TYPE  zsint_push_userlist_tab,
         ls_user_content TYPE  zsint_push_userlist.
    DATA:lv_body          TYPE string,
         lv_instance      TYPE zde_intprocessinstanceid,
         lv_alert_content TYPE string,
         lv_fname         TYPE string,
         lv_url           TYPE string,
         lv_title         TYPE string,
         lv_msg_type      TYPE string,
         lv_content       TYPE string,
         lv_date_s        TYPE string.

    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
    DATA(lv_code) = ls_detail-info-template_id.

    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
                           sendmsg = 'X' fname = 'BULELIST' ).
      RETURN.
    ENDIF.


    "审批结果
    CASE iv_type.
      WHEN 'agree'. "审批通过
        "查找申请记录
        SELECT SINGLE * FROM ztint_venpolicy INTO @DATA(ls_ztint_venpolicy) WHERE instanceid = @iv_spno.
        IF sy-subrc NE 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_specialpolicy'
                    eventdesc = '商家特殊激励申请' status = 'E' message = '未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.

        "判断是否已经处理过
        SELECT SINGLE * FROM ztint_dp_pi_h INTO CORRESPONDING FIELDS OF @ls_ztint_dp_pi_h
          WHERE processinstanceid = @iv_spno.
        IF ls_ztint_dp_pi_h-sendstatus = 'Y'. "处理过
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_specialpolicy'
                    eventdesc = '商家特殊激励申请' status = 'E' message = '实例商家特殊申请申请已处理过'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.
        lv_instance = iv_spno.
        "锁表
        CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.
        IF sy-subrc <> 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_specialpolicy'
                    eventdesc = '商家特殊激励申请' status = 'E' message = '实例商家特殊激励申请锁表失败'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.
        "抬头信息
        ls_ztint_dp_pi_h = prepare_ztint_dp_pi_h( is_detail = ls_detail iv_type = iv_type ).
        "审批详情
        LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
          CLEAR ls_ztint_dp_pi_i.
          ls_ztint_dp_pi_i = prepare_ztint_dp_pi_i( iv_spno = iv_spno is_content = ls_content ).
          APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.
        ENDLOOP.

        MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.
        MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i.
        "更新商家特殊激励申请记录
        UPDATE ztint_venpolicy SET status = 'APPROVE'
                                  msg =  '成功'
                                  approvedate = sy-datum
                                  zupd_bdate = sy-datum
                                  zupd_btime = sy-uzeit
                                  zupd_uname = sy-uname
                 WHERE guid = ls_ztint_venpolicy-guid.

        "更新审批抬头表
        UPDATE ztint_dp_inf SET  status = 'APPROVE'
                                 msg =  '成功'
                                 zupd_bdate = sy-datum
                                 zupd_btime = sy-uzeit
                                 zupd_uname = sy-uname
                WHERE guid = ls_ztint_venpolicy-guid.
        SELECT SINGLE venid,venname INTO @DATA(ls_venid) FROM ztint_ven_inf WHERE venid = @ls_ztint_venpolicy-venid.
        IF sy-subrc EQ 0.
          "获取最后审批人信息
          DATA(lv_notes) = lines( ls_detail-info-sp_record )."多少审批节点
*          DATA(lv_times) = lines( ls_detail-info-sp_record[ lv_notes ]-details )."每个审批节点几个人依次审批
*          DATA(lv_finishuser) = VALUE #( ls_detail-info-sp_record[ lv_notes ]-details[ lv_times ]-approver-userid OPTIONAL ).
          DATA(lt_time) = ls_detail-info-sp_record[ lv_notes ]-details."每个审批节点几个人依次审批
          SORT lt_time BY sptime DESCENDING.
          READ TABLE lt_time INTO DATA(ls_time) INDEX 1.
          DATA(lv_finishuser) = ls_time-approver-userid.
          IF lv_finishuser IS NOT INITIAL.
            SELECT SINGLE userid,username INTO @DATA(ls_opera_per) FROM ztint_user_inf WHERE qywxuserid = @lv_finishuser.
          ENDIF.
*****记录操作日志
          lv_content = |{ ls_opera_per-username }已批准商家({  ls_venid-venname })的特殊激励申请|.
          TRY .
              DATA(ls_opera_log) = VALUE ztint_opera_log( guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
                                                          fnam = 'M007' operatetion = 'O001' userid = ls_opera_per-userid
                                                          username = ls_opera_per-username objectid = ls_venid-venid
                                                          object = ls_venid-venname
                                                          content = lv_content operatedate = sy-datum operatetime = sy-uzeit ).
              MODIFY ztint_opera_log FROM ls_opera_log.
            CATCH cx_uuid_error.
          ENDTRY.
        ENDIF.

        "解锁
        CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.

      WHEN 'refuse'."审批拒绝
        CLEAR ls_ztint_venpolicy.
        SELECT SINGLE * FROM ztint_venpolicy INTO CORRESPONDING FIELDS OF ls_ztint_venpolicy
          WHERE instanceid = iv_spno.
        IF sy-subrc = 0.
          "更新商家准入申请记录
          UPDATE ztint_venpolicy SET status = 'UNAPPROVE'
                                    msg =  '已拒绝'
                                    zupd_bdate = sy-datum
                                    zupd_btime = sy-uzeit
                                    zupd_uname = sy-uname
                   WHERE guid = ls_ztint_venpolicy-guid.

          "更新审批抬头表
          UPDATE ztint_dp_inf SET status = 'UNAPPROVE'
                                    msg =   '已拒绝'
                                    zupd_bdate = sy-datum
                                    zupd_btime = sy-uzeit
                                    zupd_uname = sy-uname
                   WHERE guid = ls_ztint_venpolicy-guid.

          CLEAR:ls_venid,ls_opera_log,lv_content.
          SELECT SINGLE venid,venname INTO @ls_venid FROM ztint_ven_inf WHERE venid = @ls_ztint_venpolicy-venid.
          IF sy-subrc EQ 0.
            CLEAR:lv_notes,lv_finishuser,ls_time,ls_opera_per,lt_time.
            "获取最后审批人信息
            lv_notes = lines( ls_detail-info-sp_record )."多少审批节点
*            lv_times = lines( ls_detail-info-sp_record[ lv_notes ]-details )."每个审批节点几个人依次审批
*            lv_finishuser = VALUE #( ls_detail-info-sp_record[ lv_notes ]-details[ lv_times ]-approver-userid OPTIONAL ).
            lt_time = ls_detail-info-sp_record[ lv_notes ]-details."每个审批节点几个人依次审批
            SORT lt_time BY sptime DESCENDING.
            READ TABLE lt_time INTO ls_time INDEX 1.
            lv_finishuser = ls_time-approver-userid .
            IF lv_finishuser IS NOT INITIAL.
              SELECT SINGLE userid,username INTO @ls_opera_per FROM ztint_user_inf WHERE qywxuserid = @lv_finishuser.
            ENDIF.
*****记录操作日志
            lv_content = |{ ls_opera_per-username }已拒绝商家({  ls_venid-venname })的特殊激励申请|.
            TRY .
                ls_opera_log = VALUE ztint_opera_log( guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
                                                            fnam = 'M007' operatetion = 'O001' userid = ls_opera_per-userid
                                                            username = ls_opera_per-username objectid = ls_venid-venid
                                                            object = ls_venid-venname
                                                            content = lv_content operatedate = sy-datum operatetime = sy-uzeit ).
                MODIFY ztint_opera_log FROM ls_opera_log.
              CATCH cx_uuid_error.
            ENDTRY.
          ENDIF.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_specialpolicy'
                    eventdesc = '商家特殊激励申请' status = 'E' message = '更新商家特殊激励申请拒绝状态未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ENDIF.

      WHEN 'terminate'."审批撤销
        CLEAR ls_ztint_venpolicy.
        SELECT SINGLE * FROM ztint_venpolicy INTO CORRESPONDING FIELDS OF ls_ztint_venpolicy
          WHERE instanceid = iv_spno.
        IF sy-subrc = 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_specialpolicy'
                    eventdesc = '商家特殊激励申请' status = 'S' message = '更新商家特殊激励申请状态撤销'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.

          "更新商家准入申请记录
          UPDATE ztint_venpolicy SET status = 'CANCELED'
                                    msg =  '已撤销'
                                    zupd_bdate = sy-datum
                                    zupd_btime = sy-uzeit
                                    zupd_uname = sy-uname
                   WHERE guid = ls_ztint_venpolicy-guid.

          "更新审批抬头表
          UPDATE ztint_dp_inf SET status = 'CANCELED'
                                    msg =  '已撤销'
                                    zupd_bdate = sy-datum
                                    zupd_btime = sy-uzeit
                                    zupd_uname = sy-uname
                   WHERE guid = ls_ztint_venpolicy-guid.

          CLEAR:ls_venid,ls_opera_log,lv_content.
          SELECT SINGLE venid,venname INTO @ls_venid FROM ztint_ven_inf WHERE venid = @ls_ztint_venpolicy-venid.
          IF sy-subrc EQ 0.
            CLEAR ls_opera_per.
            SELECT SINGLE i~userid,i~username INTO @ls_opera_per FROM ztint_user_inf AS i
              INNER JOIN ztint_dp_inf AS f ON i~userid = f~userid
             WHERE f~guid = @ls_ztint_venpolicy-guid.
*****记录操作日志
            lv_content = |{ ls_opera_per-username }已撤销商家({  ls_venid-venname })的特殊激励申请|.
            TRY .
                ls_opera_log = VALUE ztint_opera_log( guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
                                                            fnam = 'M007' operatetion = 'O001' userid = ls_opera_per-userid
                                                            username = ls_opera_per-username objectid = ls_venid-venid
                                                            object = ls_venid-venname
                                                            content = lv_content operatedate = sy-datum operatetime = sy-uzeit ).
                MODIFY ztint_opera_log FROM ls_opera_log.
              CATCH cx_uuid_error.
            ENDTRY.
          ENDIF.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_vendor_specialpolicy'
                    eventdesc = '商家特殊激励申请' status = 'E' message = '商家特殊激励申请撤销未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ENDIF.
      WHEN OTHERS.
    ENDCASE.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPLY_YUN_FREEPOSTAGECOUPON
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
METHOD apply_yun_freepostagecoupon.

  DATA: ls_dp_inf        TYPE ztint_dp_inf,
        ls_yncoupon      TYPE ztint_yncoupon,
        lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h,
        ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
        lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i,
        ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
        lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l,
        ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
  DATA:lt_user_content TYPE  zsint_push_userlist_tab,
       ls_user_content TYPE  zsint_push_userlist.
  DATA:lv_body          TYPE string,
       lv_instance      TYPE zde_intprocessinstanceid,
       lv_alert_content TYPE string,
       lv_fname         TYPE string,
       lv_url           TYPE string,
       lv_title         TYPE string,
       lv_msg_type      TYPE string,
       lv_content       TYPE string,
       lv_date_s        TYPE string,
       lv_msg           TYPE string.

  DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
  DATA(lv_code) = ls_detail-info-template_id.
  DATA(lo_merchant) = NEW zcl_icec_merchant_api( ).
  DATA: lv_coupontype         TYPE string, "DOWNWIND 是电单送 BUS 班车送
        lv_quantity           TYPE string,
        lv_effectivebegindate TYPE string,
        lv_effectiveenddate   TYPE string.
*  DATA       lv_timestamp     TYPE tzonref-tstamps.
  DATA lv_date TYPE sy-datum.
  DATA lv_time TYPE sy-uzeit.


  IF ls_detail-errcode NE '0'.
    "没找到 记录日志
    log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
    zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
    sendmsg = 'X' fname = 'APPLYFREEPOSTAGECOUPON' ).
    RETURN.
  ENDIF.


  "审批结果
  DATA:lv_status TYPE ztint_yncoupon-status.
  CASE iv_type.
    WHEN 'agree'.
      lv_status = 'APPROVE'.
    WHEN 'refuse'.
      lv_status = 'UNAPPROVE'.
    WHEN 'terminate'.
      lv_status = 'CANCELED'.
    WHEN OTHERS.
  ENDCASE.


  lv_instance = iv_spno.

  SELECT SINGLE guid INTO @DATA(l_guid) FROM ztint_dp_inf WHERE instanceid = @lv_instance.

*  CHECK sy-subrc EQ 0.
  IF sy-subrc NE 0.

    "给表里塞数
    TRY .
        ls_dp_inf-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
      CATCH cx_uuid_error.
    ENDTRY.

    ls_dp_inf-dptype = 'APPLYFREEPOSTAGECOUPON'.
    SELECT SINGLE * INTO @DATA(ls_couponuser) FROM ztint_coupo_user WHERE fname = 'APPLYFREEPOSTAGECOUPON'. "配置的审批人虚拟账号信息
    ls_dp_inf-userid = ls_couponuser-userid.
    ls_dp_inf-deptid = ls_couponuser-deptid.

    ls_dp_inf-instanceid = iv_spno.
    SELECT SINGLE processcode INTO @ls_dp_inf-processcode FROM ztint_dp_code WHERE fname = @ls_dp_inf-dptype.

*  ls_dp_inf-businessid =
    ls_dp_inf-status = 'SUBMIT'.
*  ls_dp_inf-msg =
*  ls_dp_inf-remark =
    ls_dp_inf-zcrt_uname = ls_detail-info-applyer-userid.
    ls_dp_inf-zcrt_bdate = sy-datum.
    ls_dp_inf-zcrt_btime = sy-uzeit.
    "获取申请人
    SELECT SINGLE username INTO @DATA(lv_username)
          FROM ztint_user_inf
          WHERE qywxuserid = @ls_detail-info-applyer-userid
          AND isstill = 'X'.
    "获取返修赔信息
    lv_coupontype = 'DOWNWIND'. "默认电单送免邮券

    LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
*      "免邮券类型  DOWNWIND 是电单送  BUS 班车送
      READ TABLE ls_content-title INTO DATA(ls_title) INDEX 1.
*      IF ls_title-text = '免邮券类型'.
*        READ TABLE ls_content-value-selector-options INTO DATA(ls_option) INDEX 1.
*        IF sy-subrc = 0.
*          READ TABLE ls_option-value INTO DATA(ls_value) INDEX 1.
*          IF ls_value-text = '电单送免邮券'.
*            lv_coupontype = 'DOWNWIND'.
*          ELSEIF ls_value-text = '班车送免邮券'.
*            lv_coupontype = 'BUS'.
*          ENDIF.
*        ENDIF.

      IF ls_title-text = '维修厂Code'.
        ls_yncoupon-companycode = ls_content-value-text.
        DATA(lv_companycode) = ls_content-value-text.
        SELECT SINGLE companyid INTO @DATA(lv_companyid) FROM ztint_cus_inf
              WHERE companycode = @ls_yncoupon-companycode.
      ELSEIF ls_title-text = '维修厂名称'.
        ls_yncoupon-cusname = ls_content-value-text.
      ELSEIF ls_title-text = '发放数量'.
        ls_yncoupon-num = ls_content-value-new_number.
        lv_quantity = ls_yncoupon-num.
      ELSEIF ls_title-text = '有效期截止至'.
        CLEAR:lv_date,lv_time.
        zcl_cassint_formatter=>convert_java_timestamp_to_abap(
        EXPORTING iv_timestamp = |{ ls_content-value-date-s_timestamp }000|
        IMPORTING ev_date = lv_date ev_time = lv_time ).
        ls_yncoupon-enddate = lv_date.
        lv_effectiveenddate = |{ ls_yncoupon-enddate+0(4) }-{ ls_yncoupon-enddate+4(2) }-{ ls_yncoupon-enddate+6(2) }|.
      ELSEIF ls_title-text = '备注'.
        ls_yncoupon-remarks = ls_content-value-text.
      ENDIF.
    ENDLOOP.

    ls_dp_inf-companycode = ls_yncoupon-companycode.
    ls_dp_inf-companyname = ls_yncoupon-cusname.
    MODIFY ztint_dp_inf FROM ls_dp_inf.

    ls_yncoupon-guid = ls_dp_inf-guid.
    ls_yncoupon-deptid = ls_couponuser-deptid.
    ls_yncoupon-deptname = ls_couponuser-deptname.
    ls_yncoupon-begindate = sy-datum.  "默认发放当天即刻生效
    lv_effectivebegindate = |{ ls_yncoupon-begindate+0(4) }-{ ls_yncoupon-begindate+4(2) }-{ ls_yncoupon-begindate+6(2) }|.

    ls_yncoupon-dinguserid = ls_couponuser-dinguserid.
    ls_yncoupon-dingusername = ls_couponuser-username.
    ls_yncoupon-instanceid = iv_spno.
    ls_yncoupon-processcode = ls_dp_inf-processcode.
    ls_yncoupon-status  = 'SUBMIT'.
    ls_yncoupon-qywxuserid = ls_detail-info-applyer-userid.
    ls_yncoupon-zcrt_uname = lv_username.
    ls_yncoupon-zcrt_bdate = sy-datum.
    ls_yncoupon-zcrt_btime = sy-uzeit.

    MODIFY ztint_yncoupon FROM ls_yncoupon.

    l_guid = ls_dp_inf-guid.
  ELSE. "zrint022手动跑时

    LOOP AT ls_detail-info-apply_data-contents INTO ls_content.
*      "免邮券类型  DOWNWIND 是电单送  BUS 班车送
      READ TABLE ls_content-title INTO ls_title INDEX 1.
*      IF ls_title-text = '免邮券类型'.
*        READ TABLE ls_content-value-selector-options INTO DATA(ls_option) INDEX 1.
*        IF sy-subrc = 0.
*          READ TABLE ls_option-value INTO DATA(ls_value) INDEX 1.
*          IF ls_value-text = '电单送免邮券'.
*            lv_coupontype = 'DOWNWIND'.
*          ELSEIF ls_value-text = '班车送免邮券'.
*            lv_coupontype = 'BUS'.
*          ENDIF.
*        ENDIF.

      IF ls_title-text = '维修厂Code'.
        ls_yncoupon-companycode = ls_content-value-text.
        lv_companycode = ls_content-value-text.
        SELECT SINGLE companyid INTO @lv_companyid FROM ztint_cus_inf
              WHERE companycode = @ls_yncoupon-companycode.
      ELSEIF ls_title-text = '维修厂名称'.
        ls_yncoupon-cusname = ls_content-value-text.
      ELSEIF ls_title-text = '发放数量'.
        ls_yncoupon-num = ls_content-value-new_number.
        lv_quantity = ls_yncoupon-num.
      ELSEIF ls_title-text = '有效期截止至'.
        CLEAR:lv_date,lv_time.
        zcl_cassint_formatter=>convert_java_timestamp_to_abap(
        EXPORTING iv_timestamp = |{ ls_content-value-date-s_timestamp }000|
        IMPORTING ev_date = lv_date ev_time = lv_time ).
        ls_yncoupon-enddate = lv_date.
        lv_effectiveenddate = |{ ls_yncoupon-enddate+0(4) }-{ ls_yncoupon-enddate+4(2) }-{ ls_yncoupon-enddate+6(2) }|.
      ELSEIF ls_title-text = '备注'.
        ls_yncoupon-remarks = ls_content-value-text.
      ENDIF.
    ENDLOOP.

  ENDIF.
  "锁表
  CALL FUNCTION 'ENQUEUE_EZ_INT_DP_INF'
    EXPORTING
      mode_ztint_dp_inf = 'E'
      mandt             = sy-mandt
      guid              = l_guid.

  IF sy-subrc <> 0.
    ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'afclaim_update'
    eventdesc = '返修赔审批流状态更新' status = 'E' message = '更新审批状态锁表失败'
    key1 = iv_spno key2 = lv_code ).
    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
    RETURN.
  ENDIF.

  IF lv_companyid IS INITIAL.
    UPDATE ztint_dp_inf
    SET status = lv_status
    msg = 'failed'
    remark = '维修厂获取失败'
    zupd_bdate = sy-datum
    zupd_btime = sy-uzeit
    zupd_uname = sy-uname
    WHERE guid = l_guid.

    UPDATE ztint_yncoupon
    SET status = lv_status
    msg = 'failed'
    businessid = iv_spno
    zupd_bdate = sy-datum
    zupd_btime = sy-uzeit
    zupd_uname = sy-uname
    WHERE guid = l_guid.

    "发消息
    IF lv_companycode IS NOT INITIAL.
      lv_msg = |共享仓免邮券申请失败，维修厂CODE【{ lv_companycode }】获取失败，请检查!|.
    ELSE.
      lv_msg = |共享仓免邮券申请失败，维修厂CODE【{ ls_yncoupon-companycode }】获取失败，请检查!|.
    ENDIF.
    TRY .
        ls_ztint_dp_pi_l = VALUE ztint_dp_pi_l( guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
        event_desc = '共享仓免邮券申请同步平台'
        event_id = 'apply_yun_freepostagecoupon'
        status = 'E'
        message = lv_msg
        key_value1 = iv_spno
        key_value2 = lv_code
        zcrt_bdate = sy-datum
        zcrt_btime = sy-uzeit ).
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.

        "推送内部提醒
        "推送内部人员处理
        lv_date_s =  |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }| .

        lv_alert_content = '开思助手 \n '   && lv_msg && ' \n '
        && '实例号：' && iv_spno && ' \n ' && '流程code：' && lv_code && ' \n '
        && lv_date_s.

        lv_fname = 'APPLYFREEPOSTAGECOUPON'."配置
        lv_title =  '开思助手'.
        lv_msg_type = 'text'.

        CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
          EXPORTING
            iv_content = lv_alert_content
            iv_fname   = lv_fname.
        .
      CATCH cx_uuid_error.
    ENDTRY.
    RETURN.
  ENDIF.

  SELECT SINGLE * FROM ztint_yncoupon INTO @DATA(ls_coupon) WHERE guid = @l_guid.
  IF sy-subrc NE 0.
    UPDATE ztint_dp_inf
    SET status = lv_status
    msg = 'finish'
    zupd_bdate = sy-datum
    zupd_btime = sy-uzeit
    zupd_uname = sy-uname
    WHERE guid = l_guid.
  ELSE.

    UPDATE ztint_dp_inf
    SET status = lv_status
    msg = 'finish'
    zupd_bdate = sy-datum
    zupd_btime = sy-uzeit
    zupd_uname = sy-uname
    WHERE guid = l_guid.

    UPDATE ztint_yncoupon
    SET status = lv_status
    msg = 'finish'
    businessid = iv_spno
    zupd_bdate = sy-datum
    zupd_btime = sy-uzeit
    zupd_uname = sy-uname
    WHERE guid = l_guid.


  ENDIF.

  CALL FUNCTION 'DEQUEUE_EZ_INT_DP_INF'
    EXPORTING
      mode_ztint_dp_inf = 'E'
      mandt             = sy-mandt
      guid              = l_guid.
  "回调
  "审批结果
  DATA:lv_userloginid   TYPE string,
       lv_userloginname TYPE string,
       lv_reason        TYPE string.

  "拒绝原因
  LOOP AT ls_detail-info-sp_record INTO DATA(ls_record).
    LOOP AT ls_record-details INTO DATA(ls_recorddetail) WHERE sp_status = ls_record-sp_status.
      lv_userloginid = ls_recorddetail-approver-userid.
      SELECT SINGLE username INTO lv_userloginname FROM ztint_user_inf WHERE dinguserid = lv_userloginid AND isstill = 'X'.
      lv_reason = ls_recorddetail-speech.
    ENDLOOP.
  ENDLOOP.
  CASE iv_type.
    WHEN 'agree'.
*去除的前导零

      CALL FUNCTION 'CONVERSION_EXIT_ALPHA_OUTPUT'
        EXPORTING
          input  = lv_quantity
        IMPORTING
          output = lv_quantity.

      IF ls_couponuser-activityname IS INITIAL.
        DATA(lv_activityname) = |{ lv_username }的共享仓免邮券申请（{ iv_spno }）|.
      ELSE.
        lv_activityname = ls_couponuser-activityname.
      ENDIF.
      lv_body = '{' && |"activityName": "{ lv_activityname }",|
      && |"garageCompanyIds": ["{ lv_companyid }"],|
      && |"filePath": "",|
      && |"couponType": "{ lv_coupontype }",|
      && |"quantity": { lv_quantity },|
      && |"effectiveBeginDate": "{ lv_effectivebegindate }",|
      && |"effectiveEndDate": "{ lv_effectiveenddate }",|
      && |"operator": "{ ls_couponuser-operator }"|
      && '}'.
      lo_merchant->freepostagecoupon_complete(
      EXPORTING
        iv_body = lv_body
      IMPORTING
      ev_msg  = DATA(ev_msg)
      es_data = DATA(es_return) ).

      "回调失败则发消息并记录日志
      IF es_return IS INITIAL."表示回调失败（共享仓免邮券申请同步平台失败，请联系运维处理）
        "记录日志
        log_ztint_dp_pi_log( zeventid = 'apply_yun_freepostagecoupon' zevent = '共享仓免邮券申请同步平台'
        zmsg = '共享仓免邮券申请同步平台失败' zvalue1 = iv_spno  zvalue2 = lv_code
        sendmsg = 'X' fname = 'APPLYFREEPOSTAGECOUPON' ).
        UPDATE ztint_dp_inf
        SET status = lv_status
        msg = 'failed'
        remark = '免邮券接口回调失败'
        zupd_bdate = sy-datum
        zupd_btime = sy-uzeit
        zupd_uname = sy-uname
        WHERE guid = l_guid.

        UPDATE ztint_yncoupon
        SET status = lv_status
        msg = 'failed'
        businessid = iv_spno
        zupd_bdate = sy-datum
        zupd_btime = sy-uzeit
        zupd_uname = sy-uname
        WHERE guid = l_guid.
        RETURN.
      ENDIF.
    WHEN 'refuse'.
*      lv_body = '{' && |"oaNo": "{ iv_spno }","reason": "{ lv_reason }","troubleTicketNo": "","userLoginId": "{ lv_userloginid }","userLoginName": "{ lv_userloginname }"| && '}'.
*      lo_merchant->repair_compensation_reject(
*      EXPORTING
*        iv_body = lv_body
*      IMPORTING
*        ev_msg  = ev_msg
*        es_data = es_return ).

    WHEN 'terminate'.
    WHEN OTHERS.
  ENDCASE.



ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPROVER_FILE_TO_OBS
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* | [<---] EV_FILE                        TYPE        XSTRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD approver_file_to_obs.
    DATA:lv_file TYPE xstring.
    DATA:lv_path     TYPE string,
         lv_filename TYPE string.
    DATA:ls_log TYPE ztfi_hwobs_log .

    CHECK iv_type  EQ  'agree' . "已审批的状态

*步骤：
*第一步，监听   模版 C4ZXJuA4SfYUG6FJbwi5vGyAogcvYZZhtwGskDbyh  ，实例202504170002

    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).  "审批流的实例单号202504170002

*第二步：获取midea id

    DATA(lt_file) = ls_detail-info-apply_data-contents[ control = 'File' ]-value-files .
*
*第三步：根据midea id将excel文件转换成成二进制文件

    READ TABLE lt_file INTO DATA(ls_file) INDEX 1 .
    CHECK ls_file-file_id IS NOT INITIAL .

    DATA(lo_ref) = NEW zcl_corpweixin_api( appname = 'CASSINTQYWX'  destination = 'QYWX' ).

    CALL METHOD lo_ref->get_media_data_v2
      EXPORTING
        media   = EXACT string( ls_file-file_id )
      IMPORTING
        ev_file = lv_file.

*第四步：上传华为云obs
    "发送之前存日志
    save_basic_info(  EXPORTING is_detail = ls_detail
                      IMPORTING es_hwobs  = DATA(ls_hwobs)
     ).

    DATA(l_len) = xstrlen( lv_file ).
    lv_path = lv_filename = |cass_bill_adjust_{ sy-datum }_{ sy-uzeit }.xlsx|.

    DATA(lo_hwobs) = NEW zcl_hwobs( ).
    CALL METHOD lo_hwobs->upload_excel
      EXPORTING
        i_path           = EXACT string( lv_path ) "
        i_method         = 'POST'
        i_filename       = lv_filename "
        i_content_length = EXACT string( l_len )
        i_content_type   = 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'  "'application/vnd.ms-excel'
        i_shuiyin        = ''
        i_xstring        = lv_file
      IMPORTING
        e_hwsrc          = DATA(ev_src)
        es_msg           = DATA(es_msg).

*-记录日志
    UPDATE ztfi_hwobs_log SET code_obs    = es_msg-type
                              msg_obs     = es_msg-message
                              url         = ev_src
                              zupd_uname  = sy-uname
                              zupd_bdate  = sy-datum
                              zupd_btime  = sy-uzeit
                              WHERE guid  = ls_hwobs-guid .

    COMMIT WORK .

*第五步：发送电商KAfKA消息
    IF es_msg-type EQ 'S'.
      ls_hwobs-url = ev_src .
      CALL FUNCTION 'Z_FMFI_APPLY_FILE_HWOBS_TO_EC'
        EXPORTING
          is_hwobs = ls_hwobs.
    ENDIF.

  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPROVER_MARKET_DISTRIBUTE_V2
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
METHOD approver_market_distribute_v2.

*本流程是，阶梯返利申请备案流程达标后，BD提出新的发放审批流程，
*这里对于阶梯返利，纯发放环节

  DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h, "钉钉审批已审批抬头
        ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
        lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i, "钉钉审批已审批详情
        ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
        lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l, "钉钉审批已审批log
        ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.

  DATA:lt_user_content TYPE  zsint_push_userlist_tab,
       ls_user_content TYPE  zsint_push_userlist.

  DATA:ls_list     TYPE zcl_icec_coupon_api=>ty_send_goldcoin,
       lt_goldcoin TYPE STANDARD TABLE OF zcl_icec_coupon_api=>ty_send_goldcoin.
  DATA:lv_body          TYPE string,
       lv_timestamp     TYPE p,
       lv_instance      TYPE zde_intprocessinstanceid,
       lv_alert_content TYPE string,
       lv_fname         TYPE string,
       lv_url           TYPE string,
       lv_title         TYPE string,
       lv_msg_type      TYPE string,
       lv_content       TYPE string,
       lv_date_s        TYPE string,
       lv_companyname   TYPE string,
       lv_provideway    TYPE string.
  DATA:lt_msglist TYPE STANDARD TABLE OF ztint_msg_list,
       ls_msglist TYPE ztint_msg_list,
       ls_msg     TYPE bapiret2.
  DATA:lv_senddelay   TYPE char1, "审批后达标发放-阶梯返利（发金币和积分）
       lv_onlyapprove TYPE char1, "只审批不发放(违约金减免)
       lv_method      TYPE string,
       lv_status      TYPE string.

*IV_SPNO  审批单号实例
*IV_TYPE  agree /refuse /terminate

* 对于一线连队营销 B阶梯返利 发金币、积分 ，发放流程同意，立刻发放
* 总部营销优惠券-金币-积分，都是实时发放

  DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).  "发放审批流的实例单号202503190002
  DATA(lv_code)   = ls_detail-info-template_id. "模版code

  IF ls_detail-errcode NE '0'.
    "没找到 记录日志
    log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail_new'
                           zevent = '获取企业微信审批实例'
                             zmsg = '获取企业微信审批实例失败'
                          zvalue1 = iv_spno
                          zvalue2 = lv_code
                          sendmsg = 'X'
                            fname = 'BULELIST' ).
    RETURN.
  ENDIF.

  lv_instance = iv_spno.
  lv_status = COND #( WHEN iv_type EQ 'agree' THEN 'APPROVE'  "同意
                      WHEN iv_type EQ 'refuse' THEN  'UNAPPROVE' "驳回
                      WHEN iv_type EQ 'terminate' THEN 'CANCELED' ELSE '' ). "撤销

  "审批结果
  CASE iv_type.
    WHEN 'agree'. "审批通过

      "阶梯返利发放审批流同意即发放
      "获取发放申请记录-
      SELECT SINGLE
      CASE WHEN a~marketingtype = '2' THEN c~method_hq "总部营销
           ELSE c~process_method   "
        END AS method
      INTO @lv_method
      FROM ztint_market_req AS a
      INNER JOIN ztint_reqtype AS b
      ON a~reqtype EQ b~reqtype
      LEFT JOIN ztint_givemethod AS c
      ON a~givemethod EQ c~givemethod
      WHERE instanceid = @iv_spno.

      "判断是否已全部发放
      SELECT SINGLE sendstatus
      FROM ztint_dp_pi_h
      INTO @DATA(lv_sendstatus)
            WHERE processinstanceid = @iv_spno.  "发放流程实例编号

      IF lv_sendstatus = 'Y'. "已发放
        "已发放记录日志
        ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'send_already_new'
                                                eventdesc = '营销管理-营销发放'
                                                  status  = 'E'
                                                  message = '实例已发放过'
                                                     key1 = iv_spno
                                                     key2 = lv_code ).
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        RETURN.
      ENDIF.

*发放 发放审核流程，同意的立即发放
      "发金币 SEND_ICEC_CUSTOMER_COIN_V2
      "发积分 APPROVER_POINTS_DISTRIBUTE_V2
      CALL METHOD me->(lv_method)  "走相应的优惠券，金币，积分逻辑【ZTINT_GIVEMETHOD配置函数】
        EXPORTING
          iv_spno    = iv_spno
          iv_type    = iv_type
          iv_code    = lv_code
          is_detail  = ls_detail
        IMPORTING
          is_dp_pi_h = ls_ztint_dp_pi_h.
* ls_ztint_dp_pi_h-sendstatus  = 'N'. " Y / N /P 已发放 未发放 部分发放

    WHEN 'refuse'.   "审批拒绝
    WHEN 'terminate'."审批撤销
  ENDCASE.

  "更新表状态（发放的流程）
  SELECT SINGLE guid
  INTO @DATA(lv_guid)
        FROM ztint_dp_inf
        WHERE instanceid = @lv_instance.

  CHECK sy-subrc EQ 0.

  "锁表
  CALL FUNCTION 'ENQUEUE_EZ_INT_DP_INF'  "审批流程公用
    EXPORTING
      mode_ztint_dp_inf = 'E'
      mandt             = sy-mandt
      guid              = lv_guid.
  IF sy-subrc <> 0.
    ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'dailypoint_update_new'
                                            eventdesc = '营销管理-申请审批流状态更新'
                                               status = 'E'
                                              message = '更新审批状态锁表失败'
                                                 key1 = iv_spno
                                                 key2 = lv_code ).
    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
    RETURN.
  ENDIF.

*  SELECT COUNT(*) "不能用这个判断存在性
  SELECT SINGLE @abap_true INTO @DATA(lv_dummy)
    FROM ztint_market_req
    WHERE guid = @lv_guid.
  IF sy-subrc NE 0.
    UPDATE ztint_dp_inf
       SET status = lv_status  "审批流状态
           msg = 'finish'
           zupd_bdate = sy-datum
           zupd_btime = sy-uzeit
           zupd_uname = sy-uname
     WHERE guid = lv_guid.
  ELSE.
    UPDATE ztint_dp_inf
       SET status = lv_status
           msg = 'finish'
           zupd_bdate = sy-datum
           zupd_btime = sy-uzeit
           zupd_uname = sy-uname
     WHERE guid = lv_guid.

    IF ls_ztint_dp_pi_h-sendstatus EQ 'Y' OR ls_ztint_dp_pi_h-sendstatus EQ 'P'."Y 已发放 P 部分发放
      UPDATE ztint_market_req
         SET status = lv_status
             msg = 'finish'
             sendstatus = ls_ztint_dp_pi_h-sendstatus  " Y / N / P 已发放 未发放 部分发放【备案之后的发放审批流没有实际没有部分发放】
             senddate = sy-datum
             sendmsg = ls_ztint_dp_pi_h-sendmsg
             businessid = iv_spno
             zupd_bdate = sy-datum
             zupd_btime = sy-uzeit
             zupd_uname = sy-uname
       WHERE guid = lv_guid.
    ELSE. "N 未发放
      UPDATE ztint_market_req
         SET status = lv_status
             msg = 'finish'
             sendstatus = ls_ztint_dp_pi_h-sendstatus  " Y / N / P 已发放 未发放 部分发放
             sendmsg = ls_ztint_dp_pi_h-sendmsg
             businessid = iv_spno
             zupd_bdate = sy-datum
             zupd_btime = sy-uzeit
             zupd_uname = sy-uname
       WHERE guid = lv_guid.
    ENDIF.

  ENDIF.

  CALL FUNCTION 'DEQUEUE_EZ_INT_DP_INF'
    EXPORTING
      mode_ztint_dp_inf = 'E'
      mandt             = sy-mandt
      guid              = lv_guid.

ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPROVER_MARKET_MANAGE_PROCESS
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD approver_market_manage_process.

    "查找申请记录
    SELECT SINGLE a~givemethod,
       CASE WHEN b~process_method IS NULL THEN 'APPROVER_ONLY_NOT_SEND'  "违约金减免-只做审批不做发放
                 ELSE b~process_method
       END AS process_method
       FROM ztint_market_req AS a
       LEFT JOIN ztint_givemethod AS b  "违约金减免没有任何的发放方式
       ON a~givemethod EQ b~givemethod
       INTO @DATA(ls_ztint_req)
       WHERE instanceid = @iv_spno.
    IF sy-subrc NE 0.
      SELECT SINGLE processcode INTO @DATA(lv_code)
        FROM ztint_dp_code WHERE fname = 'MARKET_MANAGE_APPLY' .

      log_ztint_dp_pi_log(
                     zeventid = 'market_manage_process'
                     zevent = '营销管理-未找到对应的申请记录'
                     zmsg = '未找到对应的申请记录'
                     zvalue1 = iv_spno
                     zvalue2 = CONV string( lv_code )
                    ).
      RETURN.
    ELSE.  "走相应的优惠券，金币，积分逻辑

      CALL METHOD me->(ls_ztint_req-process_method)
        EXPORTING
          iv_spno = iv_spno
          iv_type = iv_type.

    ENDIF.

  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPROVER_MARKET_MANAGE_PRO_V2
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
METHOD approver_market_manage_pro_v2.

  DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h, "钉钉审批已审批抬头
        ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
        lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i, "钉钉审批已审批详情
        ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
        lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l, "钉钉审批已审批log
        ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.

  DATA:lt_user_content TYPE  zsint_push_userlist_tab,
       ls_user_content TYPE  zsint_push_userlist.

  DATA:ls_list     TYPE zcl_icec_coupon_api=>ty_send_goldcoin,
       lt_goldcoin TYPE STANDARD TABLE OF zcl_icec_coupon_api=>ty_send_goldcoin.
  DATA:lv_body          TYPE string,
       lv_timestamp     TYPE p,
       lv_instance      TYPE zde_intprocessinstanceid,
       lv_alert_content TYPE string,
       lv_fname         TYPE string,
       lv_url           TYPE string,
       lv_title         TYPE string,
       lv_msg_type      TYPE string,
       lv_content       TYPE string,
       lv_date_s        TYPE string,
       lv_companyname   TYPE string,
       lv_provideway    TYPE string.
  DATA:lt_msglist TYPE STANDARD TABLE OF ztint_msg_list,
       ls_msglist TYPE ztint_msg_list,
       ls_msg     TYPE bapiret2.
  DATA:lv_senddelay   TYPE char1, "审批后达标发放-阶梯返利（发金币和积分）
       lv_onlyapprove TYPE char1, "只审批不发放(违约金减免)
       lv_method      TYPE string,
       lv_status      TYPE string.

*IV_SPNO  审批单号实例
*IV_TYPE  agree /refuse /terminate

* 对于一线营销 B阶梯返利 发金币积分 ，流程同意，是两个月后考核达标发放
* 总部营销优惠券-金币-积分，都是实时发放

  DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).  "审批流的实例单号202503190002
  DATA(lv_code)   = ls_detail-info-template_id. "模版code

  IF ls_detail-errcode NE '0'.
    "没找到 记录日志
    log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail_new'
    zevent = '获取企业微信审批实例'
    zmsg = '获取企业微信审批实例失败'
    zvalue1 = iv_spno
    zvalue2 = lv_code
    sendmsg = 'X'
    fname = 'BULELIST' ).
    RETURN.
  ENDIF.

  lv_instance = iv_spno.
  lv_status = COND #( WHEN iv_type EQ 'agree' THEN 'APPROVE'  "同意
  WHEN iv_type EQ 'refuse' THEN  'UNAPPROVE' "驳回
  WHEN iv_type EQ 'terminate' THEN 'CANCELED' ELSE '' ). "撤销

  "审批结果
  CASE iv_type.
    WHEN 'agree'. "审批通过

      "阶梯返利同意后需要更新考核开始日期和考核结束日期
      "从申请通过当月1日开始考核
      SELECT SINGLE * INTO @DATA(ls_ztint_market_req)
            FROM ztint_market_req
            WHERE instanceid = @lv_instance.
      IF ls_ztint_market_req-reqtype EQ 'B'.
        DATA: lv_first TYPE sy-datum, "当月第一天
              lv_begin TYPE sy-datum, "考核开始日期
              lv_end   TYPE sy-datum. "考核结束日期

        lv_first = sy-datum+0(6) && '01'.
        "考核开始日期【申请通过当月1日】
        lv_begin = lv_first.

        "获取考核结束时间【当月的第一天+考核月数 再减一天】
        CALL FUNCTION 'MONTH_PLUS_DETERMINE'
          EXPORTING
            months  = ls_ztint_market_req-kpicycle
            olddate = lv_begin
          IMPORTING
            newdate = lv_end.
        lv_end = lv_end - 1.

        UPDATE ztint_market_req
        SET kpibdate = lv_begin
        kpiedate = lv_end
        WHERE instanceid = lv_instance.

      ENDIF.

      "获取申请记录-
      SELECT SINGLE
      b~senddelay,   "是否达标后发放的标识-阶梯返利
      b~onlyapprove, "是否只审批
      CASE WHEN a~marketingtype = '2' THEN c~method_hq
           ELSE c~process_method   "兼容一期的空（上线会初始化为1）
        END AS method
      INTO (@lv_senddelay,@lv_onlyapprove,@lv_method)
      FROM ztint_market_req AS a
      INNER JOIN ztint_reqtype AS b
      ON a~reqtype EQ b~reqtype
      LEFT JOIN ztint_givemethod AS c  "违约金减免没有任何的发放方式
      ON a~givemethod EQ c~givemethod
      WHERE instanceid = @iv_spno.
      IF lv_onlyapprove EQ 'X'. "只审批 的留空
        ls_ztint_dp_pi_h-sendstatus  = ''. " Y / N  已发放 未发放
        ls_ztint_dp_pi_h-sendmsg = '审批通过'.
      ELSE.

        "判断是否已全部发放
        SELECT SINGLE sendstatus
        FROM ztint_dp_pi_h
        INTO @DATA(lv_sendstatus)
              WHERE processinstanceid = @iv_spno.  "流程实例编号

        IF lv_sendstatus = 'Y'. "已发放
          "已发放记录日志
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid   = 'send_points_already_new'
          eventdesc = '营销管理-营销发放'
          status  = 'E'
          message = '实例已发放过'
          key1 = iv_spno
          key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.

*发放
        IF lv_senddelay NE 'X'.  "下面都是发放的逻辑，只有立即发放的才走(含总部营销)，阶梯返利考核合格发放的不走

          CALL METHOD me->(lv_method)  "走相应的优惠券，金币，积分逻辑【ZTINT_GIVEMETHOD配置函数】
            EXPORTING
              iv_spno    = iv_spno
              iv_type    = iv_type
              iv_code    = lv_code
              is_detail  = ls_detail
            IMPORTING
              is_dp_pi_h = ls_ztint_dp_pi_h.

        ELSE.
          ls_ztint_dp_pi_h-sendstatus  = 'N'. " Y / N /P 已发放 未发放 部分发放
          ls_ztint_dp_pi_h-sendmsg = '审批通过，考核达标后再发放'.
        ENDIF.
      ENDIF.

    WHEN 'refuse'."审批拒绝

    WHEN 'terminate'."审批撤销

  ENDCASE.

  "更新表状态
  SELECT SINGLE guid
  INTO @DATA(lv_guid)
        FROM ztint_dp_inf
        WHERE instanceid = @lv_instance.

  CHECK sy-subrc EQ 0.

  "锁表
  CALL FUNCTION 'ENQUEUE_EZ_INT_DP_INF'  "审批流程公用
    EXPORTING
      mode_ztint_dp_inf = 'E'
      mandt             = sy-mandt
      guid              = lv_guid.
  IF sy-subrc <> 0.
    ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'dailypoint_update_new'
    eventdesc = '营销管理-申请审批流状态更新'
    status = 'E'
    message = '更新审批状态锁表失败'
    key1 = iv_spno
    key2 = lv_code ).
    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
    RETURN.
  ENDIF.

*  SELECT COUNT(*) "不能用这个判断存在性
  SELECT SINGLE @abap_true INTO @DATA(lv_dummy)
    FROM ztint_market_req
    WHERE guid = @lv_guid.
  IF sy-subrc NE 0.
    UPDATE ztint_dp_inf
       SET status = lv_status  "审批流状态
           msg = 'finish'
           zupd_bdate = sy-datum
           zupd_btime = sy-uzeit
           zupd_uname = sy-uname
     WHERE guid = lv_guid.
  ELSE.
    UPDATE ztint_dp_inf
       SET status = lv_status
           msg = 'finish'
           zupd_bdate = sy-datum
           zupd_btime = sy-uzeit
           zupd_uname = sy-uname
     WHERE guid = lv_guid.

    IF ls_ztint_dp_pi_h-sendstatus EQ 'Y' OR ls_ztint_dp_pi_h-sendstatus EQ 'P'."Y 已发放 P 部分发放
      UPDATE ztint_market_req
         SET status = lv_status
             msg = 'finish'
             sendstatus = ls_ztint_dp_pi_h-sendstatus  " Y / N / P 已发放 未发放 部分发放
             senddate = sy-datum
             sendmsg = ls_ztint_dp_pi_h-sendmsg
             businessid = iv_spno
             zupd_bdate = sy-datum
             zupd_btime = sy-uzeit
             zupd_uname = sy-uname
       WHERE guid = lv_guid.
    ELSE. "N 未发放
      UPDATE ztint_market_req
         SET status = lv_status
             msg = 'finish'
             sendstatus = ls_ztint_dp_pi_h-sendstatus  " Y / N / P 已发放 未发放 部分发放
             sendmsg = ls_ztint_dp_pi_h-sendmsg
             businessid = iv_spno
             zupd_bdate = sy-datum
             zupd_btime = sy-uzeit
             zupd_uname = sy-uname
       WHERE guid = lv_guid.
    ENDIF.

  ENDIF.

  CALL FUNCTION 'DEQUEUE_EZ_INT_DP_INF'
    EXPORTING
      mode_ztint_dp_inf = 'E'
      mandt             = sy-mandt
      guid              = lv_guid.

ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPROVER_ONLY_NOT_SEND
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD approver_only_not_send.

    DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h, "钉钉审批已审批抬头
          ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
          lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i, "钉钉审批已审批详情
          ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
          lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l, "钉钉审批已审批log
          ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.

    DATA:lt_user_content TYPE  zsint_push_userlist_tab,
         ls_user_content TYPE  zsint_push_userlist.
    DATA:lv_body          TYPE string,
         lv_timestamp     TYPE p,
         lv_instance      TYPE zde_intprocessinstanceid,
         lv_alert_content TYPE string,
         lv_fname         TYPE string,
         lv_url           TYPE string,
         lv_title         TYPE string,
         lv_msg_type      TYPE string,
         lv_content       TYPE string,
         lv_date_s        TYPE string,
         lv_companyname   TYPE string,
         lv_provideway    TYPE string.
    DATA:lt_msglist TYPE STANDARD TABLE OF ztint_msg_list,
         ls_msglist TYPE ztint_msg_list.
    DATA: ls_points TYPE zsint_ml_points , "发放积分
          ls_msg    TYPE bapiret2.

* 对于违约金减免的 只做审批不发送
    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).  "审批流的实例单号202503190002
    DATA(lv_code)   = ls_detail-info-template_id. "模版code

    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail_new'
                           zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败'
                           zvalue1 = iv_spno
                           zvalue2 = lv_code
                           sendmsg = 'X'
                           fname = 'BULELIST' ).
      RETURN.
    ENDIF.

    lv_instance = iv_spno.

    DATA lv_status TYPE string.
    lv_status = COND #( WHEN iv_type EQ 'agree' THEN 'APPROVE'  "同意
                        WHEN iv_type EQ 'refuse' THEN  'UNAPPROVE' "驳回
                        WHEN iv_type EQ 'terminate' THEN 'CANCELED' ELSE '' ). "撤销

    "更新表状态
    SELECT SINGLE guid
      INTO @DATA(lv_guid)
      FROM ztint_dp_inf
      WHERE instanceid = @lv_instance.

    CHECK sy-subrc EQ 0.

    "锁表
    CALL FUNCTION 'ENQUEUE_EZ_INT_DP_INF'  "审批流程公用
      EXPORTING
        mode_ztint_dp_inf = 'E'
        mandt             = sy-mandt
        guid              = lv_guid.
    IF sy-subrc <> 0.
      ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'dailypoint_update_new'
                                                eventdesc = '营销管理-申请审批流状态更新'
                                                status = 'E'
                                                message = '更新审批状态锁表失败'
                                                key1 = iv_spno
                                                key2 = lv_code ).
      MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      RETURN.
    ENDIF.

    SELECT COUNT(*)
      FROM ztint_market_req
      WHERE guid = @lv_guid.
    IF sy-subrc NE 0.
      UPDATE ztint_dp_inf
         SET status = lv_status  "审批流状态
             msg = 'finish'
             zupd_bdate = sy-datum
             zupd_btime = sy-uzeit
             zupd_uname = sy-uname
        WHERE guid = lv_guid.
    ELSE.
      UPDATE ztint_dp_inf
           SET status = lv_status
               msg = 'finish'
               zupd_bdate = sy-datum
               zupd_btime = sy-uzeit
               zupd_uname = sy-uname
          WHERE guid = lv_guid.

      UPDATE ztint_market_req
           SET status = lv_status
               msg = 'finish'
*               sendstatus = ls_ztint_dp_pi_h-sendstatus  " Y / N  已发放 未发放
*               sendmsg = ls_ztint_dp_pi_h-sendmsg
               businessid = iv_spno
               zupd_bdate = sy-datum
               zupd_btime = sy-uzeit
               zupd_uname = sy-uname
          WHERE guid = lv_guid.
    ENDIF.

    CALL FUNCTION 'DEQUEUE_EZ_INT_DP_INF'
      EXPORTING
        mode_ztint_dp_inf = 'E'
        mandt             = sy-mandt
        guid              = lv_guid.

  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPROVER_POINTS_DISTRIBUTE_NEW
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* | [--->] IV_CODE                        TYPE        STRING(optional)
* | [--->] IV_NUMBER                      TYPE        INT4(optional)
* | [--->] IV_USERLOGINID                 TYPE        STRING(optional)
* | [--->] IS_DETAIL                      TYPE        TS_APPROVALDETAIL(optional)
* | [<---] IS_DP_PI_H                     TYPE        ZTINT_DP_PI_H
* | [<---] IV_SUCCESSNUM                  TYPE        INT4
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD approver_points_distribute_new.
*接口地址 http://ctsp.casstime.com/interface/manage/detail?id=14277&model_id=24431&classify=2442&method=POST&product_id=1
*总部营销没有批量发放积分【积分的接口不支持小数】

    DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h, "钉钉审批已审批抬头
          ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
          lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i, "钉钉审批已审批详情
          ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
          lt_ztint_dp_pi_s TYPE STANDARD TABLE OF ztint_dp_pi_s, "审批发放明细详情（优惠券、金币、积分）
          ls_ztint_dp_pi_s LIKE LINE OF lt_ztint_dp_pi_s,
          lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l, "钉钉审批已审批log
          ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.

    DATA:lt_user_content TYPE  zsint_push_userlist_tab,
         ls_user_content TYPE  zsint_push_userlist.

    DATA:lv_body          TYPE string,
         lv_timestamp     TYPE p,
         lv_instance      TYPE zde_intprocessinstanceid,
         lv_alert_content TYPE string,
         lv_fname         TYPE string,
         lv_url           TYPE string,
         lv_title         TYPE string,
         lv_msg_type      TYPE string,
         lv_content       TYPE string,
         lv_date_s        TYPE string,
         lv_companyname   TYPE string,
         lv_provideway    TYPE string.
    DATA:lt_msglist TYPE STANDARD TABLE OF ztint_msg_list,
         ls_msglist TYPE ztint_msg_list.
    DATA:ls_points TYPE zsint_ml_points , "发放积分
         lv_code   TYPE string,
         ls_msg    TYPE bapiret2.

*  B阶梯返利  发积分   ，流程同意后，是两个月后考核合格发放
*  阶梯返利不管是发金币还是积分 都是 审批通过-考核达标后发放

    IF is_detail IS INITIAL . "开始不发放，审核通过后发放时，单独调用一次【二期批量发放使用】
      DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).  "审批流的实例单号202503190002
      lv_code   = ls_detail-info-template_id. "模版code
      IF ls_detail-errcode NE '0'.
        "没找到 记录日志
        log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail_new'
                             zevent = '获取企业微信审批实例'
                             zmsg = '获取企业微信审批实例失败'
                             zvalue1 = iv_spno
                             zvalue2 = lv_code
                             sendmsg = 'X'
                             fname = 'BULELIST' ).
        RETURN.
      ENDIF.
    ELSE.
      ls_detail = is_detail .
      lv_code = iv_code .
    ENDIF.

*-判断主表是否有申请记录
    "获取申请记录
    SELECT SINGLE * FROM ztint_market_req
      INTO @DATA(ls_market_req)
      WHERE instanceid = @iv_spno.
    IF sy-subrc NE 0.
      "没找到 记录日志
      ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_points_send'
                                                eventdesc = '营销管理-发放积分'
                                                status = 'E'
                                                message = '未找到对应的申请记录'
                                                key1 = iv_spno
                                                key2 = lv_code ).
      MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      RETURN.
    ENDIF.

*判断是否已经全部发放过
    SELECT SINGLE *
      FROM ztint_dp_pi_h
      INTO CORRESPONDING FIELDS OF @ls_ztint_dp_pi_h
      WHERE processinstanceid = @iv_spno.

    IF ls_ztint_dp_pi_h-sendstatus = 'Y' "已发放过
      OR ls_market_req-sendstatus = 'Y' . "因为先备案，后发放审批流发放的，原来的备案审批流是没有发放记录的！！！
      ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_points_send'
                                                eventdesc = '营销管理-发放积分'
                                                status = 'E'
                                                message = '实例已申请发放过积分'
                                                key1 = iv_spno
                                                key2 = lv_code ).

      MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      RETURN.
    ENDIF.

    lv_instance = iv_spno.

*审批结果 agree 发放
    "锁表
    CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
      EXPORTING
        mode_ztint_dp_pi_h = 'E'
        mandt              = sy-mandt
        instanceid         = lv_instance.
    IF sy-subrc <> 0.
      "锁表失败 记录日志
      ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid   = 'lock_ztint_dp_pi_h_fail'
                                       eventdesc = '营销管理-发放积分锁表失败'
                                       status  = 'E'
                                       message = '实例发放积分锁表失败'
                                       key1 = iv_spno
                                       key2 = lv_code ).
      MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      RETURN.
    ENDIF.

    "没发过 - 抬头信息
    ls_ztint_dp_pi_h-processinstanceid = ls_detail-info-sp_no. "审批流程ID
    ls_ztint_dp_pi_h-processcode = ls_detail-info-template_id.
    ls_ztint_dp_pi_h-title = ls_detail-info-sp_name.
    ls_ztint_dp_pi_h-zresult = iv_type. "agree  审批结果
*    ls_ztint_dp_pi_h-businessid = ls_detail-business_id.
    lv_timestamp  = ls_detail-info-apply_time."创建时间
    IF lv_timestamp > 0.
      zcl_cassint_formatter=>convert_unix_timestamp_to_abap(
        EXPORTING unixsecs = lv_timestamp
        IMPORTING ev_date = ls_ztint_dp_pi_h-createdate ev_time = ls_ztint_dp_pi_h-createtime ).
      ls_ztint_dp_pi_h-createstamp = lv_timestamp.
      CONDENSE ls_ztint_dp_pi_h-createstamp.
    ENDIF.
    CLEAR lv_timestamp.

    DATA(lv_notes) = lines( ls_detail-info-sp_record )."多少审批节点

    DATA(lt_time) = ls_detail-info-sp_record[ lv_notes ]-details.
    SORT lt_time BY sptime DESCENDING.

    READ TABLE lt_time INTO DATA(ls_time) INDEX 1.
    lv_timestamp = ls_time-sptime.
    IF lv_timestamp > 0.
      zcl_cassint_formatter=>convert_unix_timestamp_to_abap(
        EXPORTING unixsecs = lv_timestamp
        IMPORTING ev_date = ls_ztint_dp_pi_h-finishdate ev_time = ls_ztint_dp_pi_h-finishtime ).
      ls_ztint_dp_pi_h-finishstamp = lv_timestamp.
      CONDENSE ls_ztint_dp_pi_h-finishstamp.
    ENDIF.
    CLEAR lv_timestamp.

    ls_ztint_dp_pi_h-status = 'finish'."流程状态
    ls_ztint_dp_pi_h-originatordeptid = ls_detail-info-applyer-partyid."发起部门
    ls_ztint_dp_pi_h-originatoruserid = ls_detail-info-applyer-userid. "申请人ID

    "审批详情
    LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
      CLEAR ls_ztint_dp_pi_i.
      ls_ztint_dp_pi_i = prepare_ztint_dp_pi_i( iv_spno = iv_spno is_content = ls_content ).
      APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.  "
    ENDLOOP.

*-已发放的清单
*  disinstanceid  发放审批流 和 自身guid对应
*  instanceid     原报备审批流

    SELECT *   "唯一一条
      FROM ztint_dp_pi_s
      INTO TABLE @DATA(lt_s)
      WHERE guid = @ls_market_req-guid."含申请流发放 && 备案后的发放审批流
    SORT lt_s BY disinstanceid .

*准备调用接口数据 - 获取积分申请记录

    "获取发起人名称
    SELECT SINGLE username,userid
       FROM ztint_user_inf
       INTO ( @DATA(lv_username),@DATA(lv_userid) )
      WHERE qywxuserid = @ls_detail-info-applyer-userid.
    IF lv_username IS INITIAL.
      lv_username = '企业微信' ." 'system' .
    ENDIF.

    ls_points-created_by  = lv_username ." 'system' .
    ls_points-order_id  = '' .

    IF iv_number NE 0.
      ls_points-point  = iv_number . "根据阶梯计算【阶梯返利考核通过外层传入】
    ELSE.
      ls_points-point  = ls_market_req-points . "根据阶梯计算【可能后期在前端界面直接输入发放积分个数】
    ENDIF.

*    ls_points-point_change_type = 'ACTIVITY' ."活动
    SELECT SINGLE sendreason INTO @ls_points-point_change_type
      FROM ztint_ml_reason
      WHERE mltype = @ls_market_req-mltype.
    IF ls_points-point_change_type IS INITIAL.
      ls_points-point_change_type = 'ACTIVITY' ."活动
    ENDIF.

    IF iv_userloginid IS NOT INITIAL. "积分发放账户
      ls_points-user_login_id = iv_userloginid.
    ELSE.
      ls_points-user_login_id = ls_market_req-userloginid.
    ENDIF.

    ls_points-remark        = ls_market_req-reqreason. "备注(BD可见用户不可见)

    SELECT SINGLE reqname   "备注-都可见 阶梯返利
      FROM ztint_reqtype
      INTO @ls_points-user_remark
      WHERE reqtype =  @ls_market_req-reqtype .

    DATA(lo_point) = NEW zcl_icec_coupon_api( ).
    CALL METHOD lo_point->send_market_points
      EXPORTING
        is_send = ls_points
      IMPORTING
        es_msg  = ls_msg.

    IF ls_msg-type = 'E'.
*      "发放积分失败
*      ls_ztint_dp_pi_h-sendstatus = 'N'. "没发放
*      ls_ztint_dp_pi_h-senddate = sy-datum.
*      ls_ztint_dp_pi_h-sendtime = sy-uzeit.
*      ls_ztint_dp_pi_h-sendmsg = '调用接口发放积分失败'.

      "记录日志
      ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'send_points_fail_new'
                                                    eventdesc = '营销管理-发放积分成功'
                                                    status = 'E'
                                                    message = '发放积分失败'
                                                    key1 = iv_spno
                                                    key2 = lv_code ).
      "推送助手值班人员处理
      CLEAR lv_date_s.
      CLEAR lv_content.
      lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

      lv_alert_content = '开思助手 \n '  && ls_ztint_dp_pi_h-title  && '审批后调用电商接口失败,请关注! \n '
      && '实例号：' && ls_ztint_dp_pi_h-processinstanceid && ' \n '
      && '流程code：' && ls_ztint_dp_pi_h-processcode && ' \n '
      && lv_date_s.

      lv_fname = 'DPAPPLYSPECIALCOUPON'."取通知优惠券的人员即可
      lv_title =  '开思助手'.
      lv_msg_type = 'text'.

      CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
        EXPORTING
          iv_content = lv_alert_content
          iv_fname   = lv_fname.

    ELSE.
      "记录日志
      ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'send_points_success_new'
                                        eventdesc = '营销管理-发放积分成功'
                                        status = 'S'
                                        message = '发放积分成功'
                                        key1 = iv_spno
                                        key3 =  EXACT string( ls_points-point ) "本次发放数量
                                        key4 = ls_points-user_login_id          "积分账户id
                                        ).

      IF ls_market_req-marketingtype = '3' . "对于发放审批流的发放，要反写报备流
        SELECT SINGLE *
          FROM ztint_market_req
          INTO @DATA(ls_source)
          WHERE instanceid = @ls_market_req-sourceinstanceid.
        IF sy-subrc = 0.
          ls_source-pointssuccess = ls_source-pointssuccess + ls_points-point.
          IF ls_source-points = ls_source-pointssuccess .
            ls_source-sendstatus = 'Y'.
            ls_source-sendmsg = '积分已发放'.
            ls_source-senddate = sy-datum.
          ELSE.
            ls_source-sendstatus = 'P'.
            ls_source-sendmsg = '积分部分发放'.
            ls_source-senddate = sy-datum.
          ENDIF.
          UPDATE ztint_market_req SET   pointssuccess = ls_source-pointssuccess
                                        sendstatus = ls_source-sendstatus
                                        sendmsg    = ls_source-sendmsg
                                        senddate = ls_source-senddate
                                  WHERE guid = ls_source-guid ."原申请流
          COMMIT WORK .
        ENDIF.
      ENDIF.

      READ TABLE lt_s INTO DATA(ls_s) WITH KEY disinstanceid = ls_market_req-instanceid
                                           BINARY SEARCH.
      IF sy-subrc = 0."读到更新【这个逻辑处理返利的多次发放】
        ls_s-pointssuccess   = ls_s-pointssuccess + ls_points-point. "已发的 + 本次成功的
        IF ls_s-points = ls_s-pointssuccess ."
          ls_s-sendstatus  = 'Y'."发放成功
        ELSE.
          ls_s-sendstatus  = 'P'."部分发放
        ENDIF.
        ls_s-zupd_bdate = sy-datum.
        ls_s-zupd_btime = sy-uzeit.
        ls_s-zupd_uname = '企业微信'.

      ELSE."没读到新增
        CLEAR:ls_s.
        ls_s-guid = ls_market_req-guid .
        ls_s-companycode = ls_market_req-companycode .
        ls_s-disinstanceid = ls_market_req-instanceid .   "发放审理流(1连队的发放是其本身)
        ls_s-instanceid = ls_market_req-sourceinstanceid ."原报备审批流
        ls_s-vtext = ls_market_req-companyname .
        ls_s-points =   ls_market_req-points."要发放
        ls_s-pointssuccess = ls_points-point."成功发放的
        ls_s-userloginid = ls_points-user_login_id. "积分发放账户

        IF ls_s-points = ls_points-point.
          ls_s-sendstatus  = 'Y'."发放成功
        ELSE.
          ls_s-sendstatus  = 'P'."部分发放
        ENDIF.

        ls_s-senddate = ls_s-zcrt_bdate = ls_s-zupd_bdate = sy-datum.
        ls_s-sendtime = ls_s-zcrt_btime = ls_s-zupd_btime = sy-uzeit.
        ls_s-zcrt_uname = ls_s-zupd_uname = '企业微信'.

      ENDIF.

      "更新成功发放数量
      ls_market_req-pointssuccess = ls_market_req-pointssuccess + ls_points-point. "发放的
      iv_successnum = ls_points-point."接口出参返回具体发放成功数量

      MODIFY ztint_dp_pi_s FROM ls_s."发放的记录表
      COMMIT WORK .
    ENDIF.

*判断整个流程的H表的发放状态
    DATA:lv_count TYPE i .
    SELECT SUM( pointssuccess )
        FROM ztint_dp_pi_s
        INTO @DATA(lv_send)  "本流程成功发放的
        WHERE guid = @ls_market_req-guid.
    IF lv_send = ls_market_req-points .  "成功发放的 = 需要发放的
      ls_market_req-sendstatus = ls_ztint_dp_pi_h-sendstatus = 'Y'."已发放
      ls_market_req-sendmsg = ls_ztint_dp_pi_h-sendmsg  = '积分发放成功'.

      "推送提醒
      IF lv_userid IS NOT INITIAL.
        "通知申请人积分额度生效
        CLEAR lv_date_s.
        CLEAR lv_content.
        lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
        lv_content =   '开思助手 \n '   && ls_ztint_dp_pi_h-title && '积分已发放，请知悉！ \n '
         && '审批编码:' && ls_ztint_dp_pi_h-processinstanceid && ' \n '
         && '客户名称:' && ls_market_req-companyname && ' \n '
         && '客户代码:' && ls_market_req-companycode && ' \n '
         && '积分:' && ls_points-point  &&  ' \n '
         && lv_date_s.

        CLEAR ls_user_content.
        ls_user_content-content = lv_content.
        ls_user_content-userid = lv_userid.
        ls_user_content-orderno = |{ ls_market_req-companycode }|.
        ls_user_content-orderdesc = |{ ls_market_req-companyname }|.
        ls_user_content-firstcatgory = '客户管理'.
        ls_user_content-secondcatgory = '客户积分发放申请审批'.
        APPEND ls_user_content TO lt_user_content.

      ENDIF.
    ELSE.
      "审批抬头表
      IF lv_send NE 0.
        ls_market_req-sendstatus = ls_ztint_dp_pi_h-sendstatus = 'P'."部分发放
        ls_market_req-sendmsg = ls_ztint_dp_pi_h-sendmsg  = '积分部分发放'.
      ELSE.
        ls_market_req-sendstatus = ls_ztint_dp_pi_h-sendstatus = 'N'."发放失败
        ls_market_req-sendmsg = ls_ztint_dp_pi_h-sendmsg = '积分发放失败'.
      ENDIF.
    ENDIF.

    ls_market_req-senddate = ls_ztint_dp_pi_h-senddate = sy-datum."发放日期
    ls_ztint_dp_pi_h-sendtime = sy-uzeit.
    ls_market_req-status = 'APPROVE'."已审批

    ls_ztint_dp_pi_h-zcrt_bdate = ls_ztint_dp_pi_h-zupd_bdate = ls_market_req-zupd_bdate = sy-datum.
    ls_ztint_dp_pi_h-zcrt_btime = ls_ztint_dp_pi_h-zupd_btime = ls_market_req-zupd_btime = sy-uzeit.
    ls_ztint_dp_pi_h-zcrt_uname = ls_ztint_dp_pi_h-zupd_uname = ls_market_req-zupd_uname = '企业微信'.

    "给值到调用点
    is_dp_pi_h  = ls_ztint_dp_pi_h .

    "保存信息
    MODIFY ztint_market_req FROM ls_market_req.      "审批流程主信息表
    MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.      "钉钉审批已审批抬头
    MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i."钉钉审批已审批详情
    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.  "记录发放日志

    "解锁表
    CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
      EXPORTING
        mode_ztint_dp_pi_h = 'E'
        mandt              = sy-mandt
        instanceid         = lv_instance.
    .
    "推送通知
    IF lt_user_content IS NOT INITIAL.
      lv_title =  '开思助手'.
      lv_msg_type = 'text'.
      CONDENSE lv_url NO-GAPS .

      CALL FUNCTION 'Z_FMQYWX_CASSINT_PUSH_MESSAGE'
*        IN UPDATE TASK
        EXPORTING
          iv_title    = lv_title
          iv_msgtype  = lv_msg_type
          iv_url      = lv_url
        TABLES
          it_userlist = lt_user_content.
    ENDIF.

  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->APPROVER_POINTS_DISTRIBUTE_V2
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* | [--->] IV_CODE                        TYPE        STRING(optional)
* | [--->] IV_POINTS                      TYPE        INT4(optional)
* | [--->] IV_USERLOGINID                 TYPE        STRING(optional)
* | [--->] IS_DETAIL                      TYPE        TS_APPROVALDETAIL(optional)
* | [<---] IS_DP_PI_H                     TYPE        ZTINT_DP_PI_H
* | [<---] IV_SUCCESSNUM                  TYPE        INT4
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD approver_points_distribute_v2.
*接口地址 http://ctsp.casstime.com/interface/manage/detail?id=14277&model_id=24431&classify=2442&method=POST&product_id=1

    DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h, "钉钉审批已审批抬头
          ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
          lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i, "钉钉审批已审批详情
          ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
          lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l, "钉钉审批已审批log
          ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.

    DATA:lt_user_content TYPE  zsint_push_userlist_tab,
         ls_user_content TYPE  zsint_push_userlist.

    DATA:lv_body          TYPE string,
         lv_timestamp     TYPE p,
         lv_instance      TYPE zde_intprocessinstanceid,
         lv_alert_content TYPE string,
         lv_fname         TYPE string,
         lv_url           TYPE string,
         lv_title         TYPE string,
         lv_msg_type      TYPE string,
         lv_content       TYPE string,
         lv_date_s        TYPE string,
         lv_companyname   TYPE string,
         lv_provideway    TYPE string.
    DATA:lt_msglist TYPE STANDARD TABLE OF ztint_msg_list,
         ls_msglist TYPE ztint_msg_list.
    DATA:ls_points TYPE zsint_ml_points , "发放积分
         lv_code   TYPE string,
         ls_msg    TYPE bapiret2.

*  B阶梯返利  发积分   ，流程同意后，是两个月后考核合格发放
*  阶梯返利不管是发金币还是积分 都是 审批通过-考核达标后发放

    IF is_detail IS INITIAL . "开始不发放，审核通过后发放时，单独调用一次【二期批量发放使用】
      DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).  "审批流的实例单号202503190002
      lv_code   = ls_detail-info-template_id. "模版code
      IF ls_detail-errcode NE '0'.
        "没找到 记录日志
        log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail_new'
                             zevent = '获取企业微信审批实例'
                             zmsg = '获取企业微信审批实例失败'
                             zvalue1 = iv_spno
                             zvalue2 = lv_code
                             sendmsg = 'X'
                             fname = 'BULELIST' ).
        RETURN.
      ENDIF.
    ELSE.
      ls_detail = is_detail .
      lv_code = iv_code .
    ENDIF.

*-判断主表是否有申请记录
    "获取申请记录
    SELECT SINGLE * FROM ztint_market_req
      INTO @DATA(ls_ztint_req)
      WHERE instanceid = @iv_spno.
    IF sy-subrc NE 0.
      "没找到 记录日志
      ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_points_send'
                                                eventdesc = '营销管理-发放积分'
                                                status = 'E'
                                                message = '未找到对应的申请记录'
                                                key1 = iv_spno
                                                key2 = lv_code ).
      MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      RETURN.
    ENDIF.

*判断是否已经全部发放过
    SELECT SINGLE *
      FROM ztint_dp_pi_h
      INTO CORRESPONDING FIELDS OF @ls_ztint_dp_pi_h
      WHERE processinstanceid = @iv_spno.

    IF ls_ztint_dp_pi_h-sendstatus = 'Y'. "已发放过
      ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_points_send'
                                                eventdesc = '营销管理-发放积分'
                                                status = 'E'
                                                message = '实例已申请发放过积分'
                                                key1 = iv_spno
                                                key2 = lv_code ).

      MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      RETURN.
    ENDIF.

    lv_instance = iv_spno.

*审批结果 agree 发放
    "锁表
    CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
      EXPORTING
        mode_ztint_dp_pi_h = 'E'
        mandt              = sy-mandt
        instanceid         = lv_instance.
    IF sy-subrc <> 0.
      "锁表失败 记录日志
      ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid   = 'lock_ztint_dp_pi_h_fail'
                                       eventdesc = '营销管理-发放积分锁表失败'
                                       status  = 'E'
                                       message = '实例发放积分锁表失败'
                                       key1 = iv_spno
                                       key2 = lv_code ).
      MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      RETURN.
    ENDIF.

    "没发过 - 抬头信息
    ls_ztint_dp_pi_h-processinstanceid = ls_detail-info-sp_no. "审批流程ID
    ls_ztint_dp_pi_h-processcode = ls_detail-info-template_id.
    ls_ztint_dp_pi_h-title = ls_detail-info-sp_name.
    ls_ztint_dp_pi_h-zresult = iv_type. "agree  审批结果
*    ls_ztint_dp_pi_h-businessid = ls_detail-business_id.
    lv_timestamp  = ls_detail-info-apply_time."创建时间
    IF lv_timestamp > 0.
      zcl_cassint_formatter=>convert_unix_timestamp_to_abap(
        EXPORTING unixsecs = lv_timestamp
        IMPORTING ev_date = ls_ztint_dp_pi_h-createdate ev_time = ls_ztint_dp_pi_h-createtime ).
      ls_ztint_dp_pi_h-createstamp = lv_timestamp.
      CONDENSE ls_ztint_dp_pi_h-createstamp.
    ENDIF.
    CLEAR lv_timestamp.

    DATA(lv_notes) = lines( ls_detail-info-sp_record )."多少审批节点

    DATA(lt_time) = ls_detail-info-sp_record[ lv_notes ]-details.
    SORT lt_time BY sptime DESCENDING.

    READ TABLE lt_time INTO DATA(ls_time) INDEX 1.
    lv_timestamp = ls_time-sptime.
    IF lv_timestamp > 0.
      zcl_cassint_formatter=>convert_unix_timestamp_to_abap(
        EXPORTING unixsecs = lv_timestamp
        IMPORTING ev_date = ls_ztint_dp_pi_h-finishdate ev_time = ls_ztint_dp_pi_h-finishtime ).
      ls_ztint_dp_pi_h-finishstamp = lv_timestamp.
      CONDENSE ls_ztint_dp_pi_h-finishstamp.
    ENDIF.
    CLEAR lv_timestamp.

    ls_ztint_dp_pi_h-status = 'finish'."流程状态
    ls_ztint_dp_pi_h-originatordeptid = ls_detail-info-applyer-partyid."发起部门
    ls_ztint_dp_pi_h-originatoruserid = ls_detail-info-applyer-userid. "申请人ID

    "审批详情
    LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
      CLEAR ls_ztint_dp_pi_i.
      ls_ztint_dp_pi_i = prepare_ztint_dp_pi_i( iv_spno = iv_spno is_content = ls_content ).
      APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.  "
    ENDLOOP.

    "准备调用接口数据 - 获取积分申请记录
    SELECT SINGLE a~mltype,
                  a~userloginid,
                  a~reqreason,
                  a~companycode,
                  a~companyname,
                  a~points,
                  b~reqname
       FROM ztint_market_req AS a
       INNER JOIN ztint_reqtype AS b
      ON a~reqtype EQ b~reqtype
      INTO @DATA(ls_req)
      WHERE instanceid = @iv_spno.

    "获取发起人名称
    SELECT SINGLE username,userid
       FROM ztint_user_inf
       INTO ( @DATA(lv_username),@DATA(lv_userid) )
      WHERE qywxuserid = @ls_detail-info-applyer-userid.
    IF lv_username IS INITIAL.
      ls_points-created_by  = '企业微信' ." 'system' .
    ELSE.
      ls_points-created_by  = lv_username .
    ENDIF.

    ls_points-order_id  = '' .

    IF iv_points NE 0.
      ls_points-point  = iv_points . "根据阶梯计算【阶梯返利考核通过外层传入】
    ELSE.
      ls_points-point  = ls_req-points . "根据阶梯计算【可能后期在前端界面直接输入发放积分个数】
    ENDIF.

*    ls_points-point_change_type = 'ACTIVITY' ."活动
    SELECT SINGLE sendreason INTO @ls_points-point_change_type FROM ztint_ml_reason WHERE mltype = @ls_req-mltype.
    IF ls_points-point_change_type IS INITIAL.
      ls_points-point_change_type = 'ACTIVITY' ."活动
    ENDIF.
    IF iv_userloginid IS NOT INITIAL.
      ls_points-user_login_id = iv_userloginid.
    ELSE.
      ls_points-user_login_id = ls_req-userloginid.
    ENDIF.
    ls_points-remark        = ls_req-reqreason.     "备注(BD可见用户不可见)
    ls_points-user_remark   = ls_req-reqname."备注-都可见 阶梯返利

    DATA(lo_point) = NEW zcl_icec_coupon_api( ).
    CALL METHOD lo_point->send_market_points
      EXPORTING
        is_send = ls_points
      IMPORTING
        es_msg  = ls_msg.

    IF ls_msg-type = 'E'.
      "发放积分失败
      ls_ztint_dp_pi_h-sendstatus = 'N'. "没发放
      ls_ztint_dp_pi_h-senddate = sy-datum.
      ls_ztint_dp_pi_h-sendtime = sy-uzeit.
      ls_ztint_dp_pi_h-sendmsg = '调用接口发放积分失败'.

      "记录日志
      ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'send_points_fail_new'
                                                    eventdesc = '营销管理-发放积分成功'
                                                    status = 'E'
                                                    message = '发放积分失败'
                                                    key1 = iv_spno
                                                    key2 = lv_code ).
      "推送助手值班人员处理
      CLEAR lv_date_s.
      CLEAR lv_content.
      lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

      lv_alert_content = '开思助手 \n '  && ls_ztint_dp_pi_h-title  && '审批后调用电商接口失败,请关注! \n '
      && '实例号：' && ls_ztint_dp_pi_h-processinstanceid && ' \n '
      && '流程code：' && ls_ztint_dp_pi_h-processcode && ' \n '
      && lv_date_s.

      lv_fname = 'DPAPPLYSPECIALCOUPON'."取通知优惠券的人员即可
      lv_title =  '开思助手'.
      lv_msg_type = 'text'.

      CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
        EXPORTING
          iv_content = lv_alert_content
          iv_fname   = lv_fname.

    ELSE.
      "发放积分成功
      ls_ztint_dp_pi_h-sendstatus = 'Y'. "已发放
      ls_ztint_dp_pi_h-senddate = sy-datum.
      ls_ztint_dp_pi_h-sendtime = sy-uzeit.
      ls_ztint_dp_pi_h-sendmsg = '发放积分成功'.

      "记录日志
      ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'send_points_success_new'
                                        eventdesc = '营销管理-发放积分成功'
                                        status = 'S'
                                        message = '发放积分成功'
                                        key1 = iv_spno
                                        key3 =  EXACT string( ls_points-point ) "本次发放数量
                                        key4 = ls_points-user_login_id "积分账户id
                                        ).
      MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      iv_successnum = ls_points-point."接口出参返回具体发放成功数量

      "更新成功发放数量
      SELECT SINGLE * INTO @DATA(ls_market_req)
        FROM ztint_market_req
        WHERE instanceid = @iv_spno.
      IF sy-subrc EQ 0.
        ls_market_req-pointssuccess = ls_market_req-pointssuccess + ls_points-point.
        IF ls_market_req-pointssuccess >= ls_market_req-points.
          ls_market_req-sendstatus = 'Y'. "已发放
        ELSE.
          ls_market_req-sendstatus = 'P'. "部分发放
        ENDIF.
        ls_market_req-senddate = sy-datum.
        MODIFY ztint_market_req  FROM ls_market_req.
      ENDIF.
      "推送提醒
      IF lv_userid IS NOT INITIAL.
        "通知申请人积分额度生效
        CLEAR lv_date_s.
        CLEAR lv_content.
        lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
        lv_content =   '开思助手 \n '   && ls_ztint_dp_pi_h-title && '积分已发放，请知悉！ \n '
         && '审批编码:' && ls_ztint_dp_pi_h-processinstanceid && ' \n '
         && '客户名称:' && ls_req-companyname && ' \n '
         && '客户代码:' && ls_req-companycode && ' \n '
         && '积分:' && ls_points-point  &&  ' \n '
         && lv_date_s.

        CLEAR ls_user_content.
        ls_user_content-content = lv_content.
        ls_user_content-userid = lv_userid.
        ls_user_content-orderno = |{ ls_req-companycode }|.
        ls_user_content-orderdesc = |{ ls_req-companyname }|.
        ls_user_content-firstcatgory = '客户管理'.
        ls_user_content-secondcatgory = '客户积分发放申请审批'.
        APPEND ls_user_content TO lt_user_content.

      ENDIF.
    ENDIF.

    "给值到调用点
    is_dp_pi_h  = ls_ztint_dp_pi_h .

    "保存信息
    MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.      "钉钉审批已审批抬头
    MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i."钉钉审批已审批详情

    "解锁表
    CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
      EXPORTING
        mode_ztint_dp_pi_h = 'E'
        mandt              = sy-mandt
        instanceid         = lv_instance.
    .
    "推送通知
    IF lt_user_content IS NOT INITIAL.
      lv_title =  '开思助手'.
      lv_msg_type = 'text'.
      CONDENSE lv_url NO-GAPS .

      CALL FUNCTION 'Z_FMQYWX_CASSINT_PUSH_MESSAGE'
*        IN UPDATE TASK
        EXPORTING
          iv_title    = lv_title
          iv_msgtype  = lv_msg_type
          iv_url      = lv_url
        TABLES
          it_userlist = lt_user_content.
    ENDIF.

  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->CLOSECREDIT_ICEC_CLOSE
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD closecredit_icec_close.

    DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h,
          ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
          lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i,
          ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
          lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l,
          ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
    DATA:lt_user_content TYPE  zsint_push_userlist_tab,
         ls_user_content TYPE  zsint_push_userlist.
    DATA:lv_body          TYPE string,
         lv_instance      TYPE zde_intprocessinstanceid,
         lv_alert_content TYPE string,
         lv_fname         TYPE string,
         lv_url           TYPE string,
         lv_title         TYPE string,
         lv_msg_type      TYPE string,
         lv_content       TYPE string,
         lv_date_s        TYPE string.

    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
    DATA(lv_code) = ls_detail-info-template_id.

    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
                           sendmsg = 'X' fname = 'BULELIST' ).
      RETURN.
    ENDIF.


    "审批结果
    CASE iv_type.
      WHEN 'agree'. "审批通过
        "查找申请记录
        SELECT SINGLE * FROM ztint_closecredi INTO @DATA(ls_ztint_closecredi)
          WHERE instanceid = @iv_spno.
        IF sy-subrc NE 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'close_credit'
                    eventdesc = '客户额度关闭' status = 'E' message = '未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.

        "判断是否已经申请过
        SELECT SINGLE * FROM ztint_dp_pi_h INTO CORRESPONDING FIELDS OF @ls_ztint_dp_pi_h
          WHERE processinstanceid = @iv_spno.
        IF ls_ztint_dp_pi_h-sendstatus = 'Y'. "已申请过
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'close_credit'
                    eventdesc = '客户额度关闭' status = 'E' message = '实例已申请过客户额度关闭'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.

        lv_instance = iv_spno.

        "锁表
        CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.
        IF sy-subrc <> 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'close_credit'
                    eventdesc = '客户额度关闭' status = 'E' message = '实例申请客户额度关闭锁表失败'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.

        DATA:lv_appUserName TYPE string, "申请人姓名
             lv_spuserId TYPE string, "审批人钉id",
             lv_spuserName TYPE ztint_user_inf-username, "审批人姓名",
             lv_speech TYPE string, "审批意见
             lv_closeReasonDetail TYPE string. "这里是具体的关闭原因详细描述"

        "抬头信息
        ls_ztint_dp_pi_h = prepare_ztint_dp_pi_h( is_detail = ls_detail iv_type = iv_type ).
        "审批详情
        LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
          CLEAR ls_ztint_dp_pi_i.
          ls_ztint_dp_pi_i = prepare_ztint_dp_pi_i( iv_spno = iv_spno is_content = ls_content ).
          APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.
        ENDLOOP.

        lv_closeReasonDetail = ls_ztint_closecredi-remarkdetail.

        "审批信息
        lv_spUserId = ls_detail-info-sp_record[ 1 ]-details[ 1 ]-approver-userid.
        SELECT SINGLE username INTO @lv_spUserName FROM ztint_user_inf WHERE dinguserid = @lv_spUserId AND isstill = 'X'.
        lv_speech = ls_detail-info-sp_record[ 1 ]-details[ 1 ]-speech.

        "获取发起人名称
        SELECT SINGLE username INTO @DATA(lv_username) FROM ztint_user_inf
          WHERE qywxuserid = @ls_detail-info-applyer-userid.
        IF lv_username IS INITIAL.
          lv_username = '企业微信'.
        ENDIF.

        "调用接口关闭额度
        lv_body = |\{"companyId":"{ ls_ztint_closecredi-companyid }", "partyIdFrom":"10424", | &&
                  |"serviceType":"{ COND #( WHEN ls_ztint_closecredi-servicetype EQ '白条'
                                                    THEN 'CASSLOAN'
                                            WHEN ls_ztint_closecredi-servicetype EQ '挂账'
                                              THEN 'COMPANY_ACCOUNT') }", | &&
                  |"appUserName":"{ lv_username }", "spuserName":"{ lv_spuserName }","speeech":"{ lv_speech }",| &&
                  | "userLoginId":"system", "closeReason":"{ ls_ztint_closecredi-remark }"\}|.
        DATA(lo_partycredit) = NEW zcl_icec_partycredit_api( ).
        lo_partycredit->close_partycredit(
          EXPORTING
            iv_data   = lv_body
          IMPORTING
            es_return = DATA(ls_return)
            ev_msg    = DATA(ls_msg)    " 返回参数
        ).
        IF ls_return-statuscode = '00'.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'close_credit'
                    eventdesc = '客户额度关闭' status = 'S' message = '调用接口客户额度关闭成功'
                    key1 = iv_spno key2 = lv_code ).
          "临时额度审批抬头表
          ls_ztint_dp_pi_h-sendstatus = 'Y'."发放成功
          ls_ztint_dp_pi_h-sendmsg = '调用接口客户额度关闭成功'.

          "临时额度信息表
          ls_ztint_closecredi-msg = '申请客户额度关闭成功'.
          ls_ztint_closecredi-status = 'APPROVE'."已审批
          ls_ztint_closecredi-zupd_bdate = sy-datum.
          ls_ztint_closecredi-zupd_btime = sy-uzeit.
          ls_ztint_closecredi-zupd_uname = '企业微信'.

          IF ls_ztint_closecredi-originatoruserid IS NOT INITIAL.
            "通知申请人额度生效
            CLEAR lv_date_s.
            CLEAR lv_content.
            lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

            lv_content =   '开思助手 \n '   && '您申请的【客户额度关闭】已成功，请知悉！ \n '
             && '客户名称：' && ls_ztint_closecredi-companyname && ' \n '
             && '客户代码：' && ls_ztint_closecredi-companycode && ' \n '
             && lv_date_s.

            CLEAR ls_user_content.
            ls_user_content-content = lv_content.
            ls_user_content-userid = ls_ztint_closecredi-originatoruserid.
            ls_user_content-orderno = |{ ls_ztint_closecredi-companycode }|.
            ls_user_content-orderdesc = |{ ls_ztint_closecredi-companyname }|.
            ls_user_content-firstcatgory = '客户'.
            ls_user_content-secondcatgory = '客户额度关闭申请审批'.
            APPEND ls_user_content TO lt_user_content.
          ENDIF.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'close_credit'
                    eventdesc = '客户额度关闭' status = 'S' message = ls_return-message
                    key1 = iv_spno key2 = lv_code ).
          "临时额度审批抬头表
          ls_ztint_dp_pi_h-sendstatus = 'N'."发放失败
          ls_ztint_dp_pi_h-sendmsg = ls_return-message.

          "临时额度信息表
          ls_ztint_closecredi-msg = ls_return-message.
          ls_ztint_closecredi-status = 'APPROVE'."已审批
          ls_ztint_closecredi-zupd_bdate = sy-datum.
          ls_ztint_closecredi-zupd_btime = sy-uzeit.
          ls_ztint_closecredi-zupd_uname = '企业微信'.

          "推送内部人员处理
          CLEAR lv_date_s.
          CLEAR lv_content.
          lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

          lv_alert_content = '开思助手 \n '  && ls_ztint_dp_pi_h-title  && ls_return-message && '  \n '
             && '实例号：' && ls_ztint_dp_pi_h-processinstanceid && ' \n '
             && '流程code：' && ls_ztint_dp_pi_h-processcode && ' \n '
             && lv_date_s.

          lv_fname = 'DPAPPLYCLOSECREDIT'.
          lv_title =  '开思助手'.
          lv_msg_type = 'text'.

          CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
            EXPORTING
              iv_content = lv_alert_content
              iv_fname   = lv_fname.

        ENDIF.

        "更新表
        MODIFY ztint_closecredi FROM ls_ztint_closecredi.
        MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i.
        "锁表
        CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.

*   推送通知
        IF lt_user_content IS NOT INITIAL.
          lv_title =  '开思助手'.
          lv_msg_type = 'text'.
          CONDENSE lv_url NO-GAPS .

          CALL FUNCTION 'Z_FMQYWX_CASSINT_PUSH_MESSAGE'
            IN UPDATE TASK
            EXPORTING
              iv_title    = lv_title
              iv_msgtype  = lv_msg_type
              iv_url      = lv_url
            TABLES
              it_userlist = lt_user_content.
        ENDIF.
      WHEN 'refuse'."审批拒绝
        CLEAR ls_ztint_closecredi.
        SELECT SINGLE * FROM ztint_closecredi INTO CORRESPONDING FIELDS OF ls_ztint_closecredi
          WHERE instanceid = iv_spno.
        IF sy-subrc = 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'close_credit'
                    eventdesc = '客户额度关闭' status = 'S' message = '更新客户额度关闭申请状态'
                    key1 = iv_spno key2 = lv_code ).

          ls_ztint_closecredi-status = 'UNAPPROVE'.
          ls_ztint_closecredi-msg = '审批拒绝'.
          ls_ztint_closecredi-zupd_bdate = sy-datum.
          ls_ztint_closecredi-zupd_btime = sy-uzeit.
          ls_ztint_closecredi-zupd_uname = '企业微信'.

          MODIFY ztint_closecredi FROM ls_ztint_closecredi.
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'close_credit'
                    eventdesc = '客户额度关闭' status = 'S' message = '客户额度关闭申请拒绝未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ENDIF.
      WHEN 'terminate'."审批撤销
        CLEAR ls_ztint_closecredi.
        SELECT SINGLE * FROM ztint_closecredi INTO CORRESPONDING FIELDS OF ls_ztint_closecredi
          WHERE instanceid = iv_spno.
        IF sy-subrc = 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'close_credit'
                    eventdesc = '客户额度关闭' status = 'S' message = '更新客户额度关闭申请状态撤销'
                    key1 = iv_spno key2 = lv_code ).

          ls_ztint_closecredi-status = 'CANCELED'.
          ls_ztint_closecredi-msg = '审批拒绝'.
          ls_ztint_closecredi-zupd_bdate = sy-datum.
          ls_ztint_closecredi-zupd_btime = sy-uzeit.
          ls_ztint_closecredi-zupd_uname = '企业微信'.

          MODIFY ztint_closecredi FROM ls_ztint_closecredi.
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'close_credit'
                    eventdesc = '客户额度关闭' status = 'S' message = '客户额度关闭申请撤销未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ENDIF.

      WHEN OTHERS.
    ENDCASE.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->CONSTRUCTOR
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_TOKEN                       TYPE        ZTICERP_DING_TK-ACCESS_TOKEN(optional)
* | [--->] IV_DESTINATION                 TYPE        RFCDES-RFCDEST
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD constructor.
    IF iv_token IS NOT INITIAL.
      mv_agenttoken = iv_token.
    ELSE.
      DATA(lo_qywx) = NEW zcl_corpweixin_api( appname = 'CASSINTQYWX' destination = 'QYWX' ).
      DO 5 TIMES.
        DATA(lv_token) = lo_qywx->get_corpwx_token( ).
        IF lv_token NE ''.
          EXIT.
        ENDIF.
      ENDDO.
      mv_agenttoken = lv_token.
    ENDIF.
    mv_destination = iv_destination.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->DELAY_ICEC_CREDIT_CLOSE
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD delay_icec_credit_close.

    DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h,
          ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
          lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i,
          ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
          lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l,
          ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
    DATA:lt_user_content TYPE  zsint_push_userlist_tab,
         ls_user_content TYPE  zsint_push_userlist.
    DATA:lv_body          TYPE string,
         lv_instance      TYPE zde_intprocessinstanceid,
         lv_alert_content TYPE string,
         lv_fname         TYPE string,
         lv_url           TYPE string,
         lv_title         TYPE string,
         lv_msg_type      TYPE string,
         lv_content       TYPE string,
         lv_date_s        TYPE string.

    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
    DATA(lv_code) = ls_detail-info-template_id.
    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
                           sendmsg = 'X' fname = 'BULELIST' ).
      RETURN.
    ENDIF.

    "审批结果
    CASE iv_type.
      WHEN 'agree'. "审批通过
        SELECT SINGLE * FROM ztint_creditdc INTO @DATA(ls_ztint_creditdc)
          WHERE instanceid = @iv_spno.
        IF sy-subrc NE 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'delay_closecredit'
                            eventdesc = '延迟额度自动关闭' status = 'E' message = '未找到对应的申请记录'
                            key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.

        "判断是否已经申请过
        SELECT SINGLE * FROM ztint_dp_pi_h INTO CORRESPONDING FIELDS OF @ls_ztint_dp_pi_h
          WHERE processinstanceid = @iv_spno.
        IF ls_ztint_dp_pi_h-sendstatus = 'Y'. "已申请过
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'delay_closecredit'
                            eventdesc = '延迟额度自动关闭' status = 'E' message = '实例已申请过延迟额度自动关闭'
                            key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.
        lv_instance = iv_spno.
        "锁表
        CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.
        IF sy-subrc <> 0.
          "锁表失败 记录日志
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'delay_closecredit'
                            eventdesc = '延迟额度自动关闭' status = 'E' message = '实例延迟额度自动关闭锁表失败'
                            key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.
        ls_ztint_dp_pi_h = prepare_ztint_dp_pi_h( is_detail = ls_detail iv_type = iv_type ).
        "审批详情
        LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
          CLEAR ls_ztint_dp_pi_i.
          ls_ztint_dp_pi_i = prepare_ztint_dp_pi_i( iv_spno = iv_spno is_content = ls_content ).
          APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.
        ENDLOOP.

        SELECT SINGLE value INTO @DATA(lv_days) FROM ztint_par WHERE fname = 'CREDITDELAYCLOSEDAYS'.
        lv_body = |\{"companyId":"{ ls_ztint_creditdc-companyid }","delayDay":"{ lv_days }"\}|.
        NEW zcl_icec_partycredit_api( )->delay_credit_info(
          EXPORTING
            iv_body        = lv_body
          IMPORTING
            ev_msg         = DATA(ls_msg)    " 返回参数
            es_delaycredit = DATA(ls_delaycredit)
        ).

        IF ls_delaycredit-status = 'true' OR ls_delaycredit-status EQ 'X'.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'delay_closecredit'
                            eventdesc = '延迟额度自动关闭' status = 'S' message = '调用接口额度延迟关闭成功'
                            key1 = iv_spno key2 = lv_code ).

          "临时额度审批抬头表
          ls_ztint_dp_pi_h-sendstatus = 'Y'."发放成功
          ls_ztint_dp_pi_h-sendmsg = '调用接口额度延迟关闭成功'.

          "临时额度信息表
          ls_ztint_creditdc-msg = '额度延迟关闭成功'.
          ls_ztint_creditdc-status = 'APPROVE'."已审批
          ls_ztint_creditdc-zupd_bdate = sy-datum.
          ls_ztint_creditdc-zupd_btime = sy-uzeit.
          ls_ztint_creditdc-zupd_uname = '企业微信'.

          IF ls_ztint_creditdc-originatoruserid IS NOT INITIAL.
            "通知申请人额度生效
            CLEAR lv_date_s.
            CLEAR lv_content.
            lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

            lv_content =   '开思助手 \n '   && '您申请的【额度延迟关闭】已成功，请知悉！ \n '
             && '客户名称：' && ls_ztint_creditdc-companyname && ' \n '
             && '客户代码：' && ls_ztint_creditdc-companycode && ' \n '
             && lv_date_s.

            CLEAR ls_user_content.
            ls_user_content-content = lv_content.
            ls_user_content-userid = ls_ztint_creditdc-originatoruserid.
            ls_user_content-orderno = |{ ls_ztint_creditdc-companycode }|.
            ls_user_content-orderdesc = |{ ls_ztint_creditdc-companyname }|.
            ls_user_content-firstcatgory = '客户管理'.
            ls_user_content-secondcatgory = '额度延迟关闭申请审批'.
            APPEND ls_user_content TO lt_user_content.
          ENDIF.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'delay_closecredit'
                            eventdesc = '延迟额度自动关闭' status = 'S' message = '调用接口额度延迟关闭失败'
                            key1 = iv_spno key2 = lv_code ).

          "临时额度审批抬头表
          ls_ztint_dp_pi_h-sendstatus = 'N'."发放成功
          IF ls_delaycredit-msg IS NOT  INITIAL.
            ls_ztint_dp_pi_h-sendmsg = ls_delaycredit-msg.
          ELSE.
            ls_ztint_dp_pi_h-sendmsg = '调用接口额度延迟关闭失败'.
          ENDIF.

          "临时额度信息表
          ls_ztint_creditdc-msg = ls_ztint_dp_pi_h-sendmsg.
          ls_ztint_creditdc-status = 'APPROVE'."已审批
          ls_ztint_creditdc-zupd_bdate = sy-datum.
          ls_ztint_creditdc-zupd_btime = sy-uzeit.
          ls_ztint_creditdc-zupd_uname = '企业微信'.

          CLEAR lv_date_s.
          CLEAR lv_content.
          lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
          lv_alert_content = '开思助手 \n '  && ls_ztint_dp_pi_h-title  && '审批后调用电商接口失败,请关注! \n '
                            && '实例号：' && ls_ztint_dp_pi_h-processinstanceid && ' \n '
                            && '流程code：' && ls_ztint_dp_pi_h-processcode && ' \n '
                            && lv_date_s.

          lv_fname = 'DPCREDITDELAYCLOSE'.
          lv_title =  '开思助手'.
          lv_msg_type = 'text'.

          CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
            EXPORTING
              iv_content = lv_alert_content
              iv_fname   = lv_fname
*             IV_URL     =
            .

***          CALL FUNCTION 'Z_FMINT_ERRORALERT'
***            IN UPDATE TASK
***            EXPORTING
***              iv_title    = lv_title
***              iv_msg_type = lv_msg_type
****             IV_URL      =
***              iv_content  = lv_alert_content
***              iv_fname    = lv_fname.
        ENDIF.

        "更新表
        MODIFY ztint_creditdc FROM ls_ztint_creditdc.
        MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.
        MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i.
        MODIFY ztint_dp_pi_l FROM TABLE lt_ztint_dp_pi_l.
        "锁表
        CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.

*   推送通知
        IF lt_user_content IS NOT INITIAL.
          lv_title =  '开思助手'.
          lv_msg_type = 'text'.
          CONDENSE lv_url NO-GAPS .

          CALL FUNCTION 'Z_FMQYWX_CASSINT_PUSH_MESSAGE'
            IN UPDATE TASK
            EXPORTING
              iv_title    = lv_title
              iv_msgtype = lv_msg_type
              iv_url      = lv_url
            TABLES
              it_userlist = lt_user_content.
        ENDIF.

      WHEN 'refuse'."审批拒绝
        CLEAR ls_ztint_creditdc.
        SELECT SINGLE * FROM ztint_creditdc INTO CORRESPONDING FIELDS OF ls_ztint_creditdc
          WHERE instanceid = iv_spno.
        IF sy-subrc = 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'delay_closecredit'
                            eventdesc = '延迟额度自动关闭' status = 'S' message = '更新延迟额度自动关闭状态'
                            key1 = iv_spno key2 = lv_code ).

          ls_ztint_creditdc-status = 'UNAPPROVE'.
          ls_ztint_creditdc-msg = '审批拒绝'.
          ls_ztint_creditdc-zupd_bdate = sy-datum.
          ls_ztint_creditdc-zupd_btime = sy-uzeit.
          ls_ztint_creditdc-zupd_uname = '企业微信'.

          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          MODIFY ztint_creditdc FROM ls_ztint_creditdc.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'delay_closecredit'
                            eventdesc = '延迟额度自动关闭' status = 'S' message = '延迟额度自动关闭拒绝未找到对应的申请记录'
                            key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ENDIF.
      WHEN 'terminate'."审批撤销
        CLEAR ls_ztint_creditdc.
        SELECT SINGLE * FROM ztint_creditdc INTO CORRESPONDING FIELDS OF ls_ztint_creditdc
          WHERE instanceid = iv_spno.
        IF sy-subrc = 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'delay_closecredit'
                            eventdesc = '延迟额度自动关闭' status = 'S' message = '更新延迟额度自动关闭撤销'
                            key1 = iv_spno key2 = lv_code ).
          ls_ztint_creditdc-status = 'CANCELED'.
          ls_ztint_creditdc-msg = '已撤销'.
          ls_ztint_creditdc-zupd_bdate = sy-datum.
          ls_ztint_creditdc-zupd_btime = sy-uzeit.
          ls_ztint_creditdc-zupd_uname = '企业微信'.

          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          MODIFY ztint_creditdc FROM ls_ztint_creditdc.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'delay_closecredit'
                            eventdesc = '延迟额度自动关闭' status = 'S' message = '延迟额度自动关闭撤销未找到对应的申请记录'
                            key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ENDIF.
      WHEN OTHERS.
    ENDCASE.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->GET_APPROVAL_DETAIL_BY_SPNO
* +-------------------------------------------------------------------------------------------------+
* | [--->] SPNO                           TYPE        STRING
* | [<-()] APPROVAL_DETAIL                TYPE        TS_APPROVALDETAIL
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD get_approval_detail_by_spno.
    DATA:
      lv_alert_content TYPE string,
      lv_fname         TYPE string,
      lv_msg_type      TYPE string,
      lv_title         TYPE string.

    DATA: ls_content_type TYPE zapi_s_contenttype,
          ls_log          TYPE zticcrm_log.
    DATA:lv_uristr TYPE string VALUE '/cgi-bin/oa/getapprovaldetail?access_token={ACCESS_TOKEN}'.
    DATA:lv_body TYPE string,
         lv_uri  TYPE string.

    DATA(lo_api) = NEW zcl_icec_api( ).
    DATA(lo_qywx) = NEW zcl_corpweixin_api( appname = 'CASSINTQYWX' destination = 'QYWX' ).
    "调用接口
    DO 5 TIMES.
      CLEAR:lv_body,lv_uri.
      lv_uri = lv_uristr.

      DO 5 TIMES.
        DATA(lv_token) = lo_qywx->get_corpwx_token( ).
        IF lv_token NE ''.
          EXIT.
        ENDIF.
      ENDDO.

      REPLACE '{ACCESS_TOKEN}' IN lv_uri WITH lv_token.
      ls_content_type-content_type = 'application/json;charset=UTF-8'.
      lv_body = |\{"sp_no":"{ spno }"\}|.

      WAIT UP TO 1 SECONDS.

      CALL METHOD lo_api->post_data(
        EXPORTING
          iv_rfcdest      = mv_destination
          iv_uri          = lv_uri
          is_content_type = ls_content_type
          iv_body         = lv_body
        IMPORTING
          json_out        = DATA(lv_out)
          ev_msg          = DATA(ls_msg)
                            ).
      save_api_log( EXPORTING iv_keyvalue = lv_uri ev_msg = ls_msg  iv_requestbody = lv_body iv_responsebody = lv_out ).
      IF lv_out IS NOT INITIAL.
        "解析json
        /ui2/cl_json=>deserialize(
                    EXPORTING
                       json = lv_out
                    CHANGING
                       data = approval_detail ).

      ENDIF.

      IF approval_detail-errcode EQ '0'.
        EXIT.
      ENDIF.
      CLEAR:lv_out,ls_msg,lv_token,approval_detail.
    ENDDO.

    IF approval_detail-info-template_id IS INITIAL.
      lv_alert_content = '开思助手 \n '  && '审批流：' && spno
           && '回调获取审批流详情失败,请关注! \n '
           && |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

      lv_fname = 'ALL'.
      lv_title =  '开思助手'.
      lv_msg_type = 'text'.

      CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
        EXPORTING
          iv_content = lv_alert_content
          iv_fname   = lv_fname.
    ENDIF.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Private Method ZCL_CORPWEIXIN_APPROVAL_API->LOG
* +-------------------------------------------------------------------------------------------------+
* | [--->] IS_LOG                         TYPE        ZTICCRM_LOG
* | [--->] IV_COMMIT                      TYPE        CHAR1 (default ='')
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD LOG.

    DATA: ls_db TYPE zticcrm_log.

    MOVE-CORRESPONDING is_log TO ls_db.

    "guid CX_UUID_ERROR
    TRY .
        ls_db-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
      CATCH cx_uuid_error.
        EXIT.

    ENDTRY.

    ls_db-zcrt_uname = sy-uname.
    ls_db-zcrt_bdate = sy-datum.
    ls_db-zcrt_btime = sy-uzeit.

    MODIFY zticcrm_log FROM ls_db.

    IF iv_commit = 'X'.
      COMMIT WORK AND WAIT.
    ENDIF.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Private Method ZCL_CORPWEIXIN_APPROVAL_API->LOG_ZTINT_DP_PI_LOG
* +-------------------------------------------------------------------------------------------------+
* | [--->] ZEVENTID                       TYPE        STRING(optional)
* | [--->] ZEVENT                         TYPE        STRING(optional)
* | [--->] ZMSG                           TYPE        STRING(optional)
* | [--->] ZVALUE1                        TYPE        STRING(optional)
* | [--->] ZVALUE2                        TYPE        STRING(optional)
* | [--->] SENDMSG                        TYPE        STRING(optional)
* | [--->] FNAME                          TYPE        STRING(optional)
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD LOG_ZTINT_DP_PI_LOG.
    DATA:lv_fname    TYPE string,
         lv_title    TYPE string,
         lv_msg_type TYPE string.
    TRY .
        DATA(ls_ztint_dp_pi_l) = VALUE ztint_dp_pi_l( guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
                                                      event_desc = zevent
                                                      event_id = zeventid
                                                      status = 'E'
                                                      message = zmsg
                                                      key_value1 = zvalue1
                                                      key_value2 = zvalue2
                                                      zcrt_bdate = sy-datum
                                                     zcrt_btime = sy-uzeit ).
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        IF sendmsg IS NOT INITIAL.
          "推送内部提醒
          "推送内部人员处理
          DATA(lv_date_s) =  |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }| .

          DATA(lv_alert_content) = '开思助手 \n '   && '审批流回调失败,请关注! \n '
                            && '实例号：' && zvalue1 && ' \n ' && '流程code：' && zvalue2 && ' \n '
                            && lv_date_s.

          lv_fname = fname."配置
          lv_title =  '开思助手'.
          lv_msg_type = 'text'.

          CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
            EXPORTING
              iv_content = lv_alert_content
              iv_fname   = lv_fname.
        ENDIF.       .
     CATCH cx_uuid_error.
    ENDTRY.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Private Method ZCL_CORPWEIXIN_APPROVAL_API->PREPARE_ZTINT_DP_COIN_L
* +-------------------------------------------------------------------------------------------------+
* | [--->] EVENTID                        TYPE        STRING
* | [--->] EVENTDESC                      TYPE        STRING
* | [--->] STATUS                         TYPE        STRING
* | [--->] MESSAGE                        TYPE        STRING
* | [--->] KEY1                           TYPE        STRING(optional)
* | [--->] KEY2                           TYPE        STRING(optional)
* | [--->] KEY3                           TYPE        STRING(optional)
* | [--->] KEY4                           TYPE        STRING(optional)
* | [--->] KEY5                           TYPE        STRING(optional)
* | [<-()] DP_PI_L                        TYPE        ZTINT_DP_PI_L
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD prepare_ztint_dp_coin_l.
    TRY .
        dp_pi_l-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
      CATCH cx_uuid_error.
    ENDTRY.
    dp_pi_l-event_id = eventid.
    dp_pi_l-event_desc = eventdesc.
    dp_pi_l-status = status.
    dp_pi_l-message = message.
    dp_pi_l-key_value1 = key1.
    dp_pi_l-key_value2 = key2.
    dp_pi_l-key_value3 = key3.
    dp_pi_l-key_value4 = key4.
    dp_pi_l-key_value5 = key5.
    dp_pi_l-zcrt_bdate = sy-datum.
    dp_pi_l-zcrt_btime = sy-uzeit.

  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Private Method ZCL_CORPWEIXIN_APPROVAL_API->PREPARE_ZTINT_DP_PI_H
* +-------------------------------------------------------------------------------------------------+
* | [--->] IS_DETAIL                      TYPE        TS_APPROVALDETAIL
* | [--->] IV_TYPE                        TYPE        STRING
* | [<-()] DP_PI_H                        TYPE        ZTINT_DP_PI_H
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD prepare_ztint_dp_pi_h.

    DATA: lv_timestamp TYPE p.
    "没增加过
    "抬头信息赋值
    dp_pi_h-processinstanceid = is_detail-info-sp_no.
    dp_pi_h-processcode = is_detail-info-template_id.
    dp_pi_h-title = is_detail-info-sp_name.
*        dp_pi_h-businessid = is_detail-business_id.
    dp_pi_h-zresult = iv_type.
    lv_timestamp  = is_detail-info-apply_time."创建时间
    IF lv_timestamp > 0.
      zcl_cassint_formatter=>convert_unix_timestamp_to_abap(
        EXPORTING unixsecs = lv_timestamp
        IMPORTING ev_date = dp_pi_h-createdate ev_time = dp_pi_h-createtime ).
      dp_pi_h-createstamp = lv_timestamp.
      CONDENSE dp_pi_h-createstamp.
    ENDIF.
    CLEAR lv_timestamp.

    DATA(lv_notes) = lines( is_detail-info-sp_record )."多少审批节点
    DATA(lt_time) = is_detail-info-sp_record[ lv_notes ]-details ."每个审批节点几个人依次审批
    SORT lt_time BY sptime DESCENDING.
    READ TABLE lt_time INTO DATA(ls_time) INDEX 1.
*    DATA(lv_time) = lines( is_detail-info-sp_record[ lv_notes ]-details )."每个审批节点几个人依次审批
    lv_timestamp = ls_time-sptime.
    IF lv_timestamp > 0.
      zcl_cassint_formatter=>convert_unix_timestamp_to_abap(
        EXPORTING unixsecs = lv_timestamp
        IMPORTING ev_date = dp_pi_h-finishdate ev_time = dp_pi_h-finishtime ).
      dp_pi_h-finishstamp = lv_timestamp.
      CONDENSE dp_pi_h-finishstamp.
    ENDIF.

    CLEAR lv_timestamp.
    dp_pi_h-status = 'finish'.
    dp_pi_h-originatordeptid = is_detail-info-applyer-partyid.
    dp_pi_h-originatoruserid = is_detail-info-applyer-userid.
    dp_pi_h-senddate = sy-datum.
    dp_pi_h-sendtime = sy-uzeit.
    dp_pi_h-zcrt_bdate = dp_pi_h-zupd_bdate	= sy-datum.
    dp_pi_h-zcrt_btime = dp_pi_h-zupd_btime	= sy-uzeit.

  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Private Method ZCL_CORPWEIXIN_APPROVAL_API->PREPARE_ZTINT_DP_PI_I
* +-------------------------------------------------------------------------------------------------+
* | [--->] IS_CONTENT                     TYPE        TS_APPLYDATACONTENT
* | [--->] IV_SPNO                        TYPE        STRING
* | [<-()] DP_PI_I                        TYPE        ZTINT_DP_PI_I
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD prepare_ztint_dp_pi_i.
    dp_pi_i-processinstanceid = iv_spno.
    dp_pi_i-posnr = sy-tabix.
    dp_pi_i-name = VALUE #( is_content-title[ lang = 'zh_CN' ]-text OPTIONAL ).
    CASE is_content-control.
      WHEN 'Text' OR 'Textarea'.
        dp_pi_i-value = is_content-value-text.
      WHEN 'Number'.
        dp_pi_i-value = is_content-value-new_number.
      WHEN 'Money'.
        dp_pi_i-value = is_content-value-new_money.
      WHEN 'Selector'.
        dp_pi_i-value = VALUE #( is_content-value-selector-options[ 1 ]-value[ 1 ]-text OPTIONAL ).
      WHEN  OTHERS.
    ENDCASE.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Private Method ZCL_CORPWEIXIN_APPROVAL_API->PREPARE_ZTINT_DP_PI_L
* +-------------------------------------------------------------------------------------------------+
* | [--->] EVENTID                        TYPE        STRING
* | [--->] EVENTDESC                      TYPE        STRING
* | [--->] STATUS                         TYPE        STRING
* | [--->] MESSAGE                        TYPE        STRING
* | [--->] KEY1                           TYPE        STRING(optional)
* | [--->] KEY2                           TYPE        STRING(optional)
* | [--->] KEY3                           TYPE        STRING(optional)
* | [--->] KEY4                           TYPE        STRING(optional)
* | [--->] KEY5                           TYPE        STRING(optional)
* | [--->] CONTENT                        TYPE        STRING(optional)
* | [<-()] DP_PI_L                        TYPE        ZTINT_DP_PI_L
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD prepare_ztint_dp_pi_l.
    TRY .
        dp_pi_l-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
      CATCH cx_uuid_error.
    ENDTRY.
    dp_pi_l-event_id = eventid.
    dp_pi_l-event_desc = eventdesc.
    dp_pi_l-status = status.
    dp_pi_l-message = message.
    dp_pi_l-key_value1 = key1.
    dp_pi_l-key_value2 = key2.
    dp_pi_l-key_value3 = key3.
    dp_pi_l-key_value4 = key4.
    dp_pi_l-key_value5 = key5.
    dp_pi_l-content    = content.
    dp_pi_l-zcrt_bdate = sy-datum.
    dp_pi_l-zcrt_btime = sy-uzeit.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->PROCESS_APPROVAL
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TEMPLATEID                  TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD process_approval.
    CHECK iv_spno IS NOT INITIAL AND iv_templateid IS NOT INITIAL.
    DATA:ls_spnolog TYPE ztint_spno_log.
    ls_spnolog-instanceid = iv_spno.
    "对审批流加锁，防止同时多个进程处理一个审批流
    DO 3 TIMES.
      DATA(lv_index) = sy-index.
      CALL FUNCTION 'ENQUEUE_EZTINT_SPNO_LOG'
        EXPORTING
          mode_ztint_spno_log = 'E'
          mandt               = sy-mandt
          instanceid          = ls_spnolog-instanceid
        EXCEPTIONS
          foreign_lock        = 1
          system_failure      = 2
          OTHERS              = 3.
      IF sy-subrc <> 0.
        WAIT UP TO lv_index SECONDS.
      ELSE.
        EXIT.
      ENDIF.
    ENDDO.

    SELECT SINGLE instanceid INTO @DATA(lv_instan) FROM ztint_spno_log WHERE instanceid = @iv_spno.
    CHECK sy-subrc NE 0.

    SELECT * INTO TABLE @DATA(lt_fur) FROM ztcrm_ding_p_fur
      WHERE process_code = @iv_templateid AND process_type = @iv_type AND process_on = 'X'.
    IF sy-subrc = 0.
      SORT lt_fur BY process_code process_type process_seq.

      LOOP AT lt_fur INTO DATA(ls_fur).
        CALL METHOD me->(ls_fur-process_method)
          EXPORTING
            iv_spno = iv_spno
            iv_type = iv_type.

        CLEAR ls_fur.
      ENDLOOP.
    ENDIF.

    "推送审批结果到电商
    IF iv_type = 'agree'.
      DATA: lv_msg TYPE string.

      SELECT SINGLE * FROM ztint_dp_icec_cf INTO @DATA(ls_ztint_dp_icec_cf)
        WHERE qywx_processcode = @iv_templateid AND process_on = 'X'.
      IF sy-subrc = 0.
        "判断是否推送过
        SELECT SINGLE * FROM ztint_dp_icec_l INTO @DATA(ls_ztint_dp_icec_l)
          WHERE instanceid = @iv_spno.
        IF sy-subrc NE 0.
          "未推送过
          send_dpinfo_to_icec( iv_spno = iv_spno ).
        ENDIF.
      ENDIF.
    ENDIF.

    ls_spnolog-processcode = iv_templateid.
    ls_spnolog-zcrt_bdate = sy-datum.
    ls_spnolog-zcrt_btime = sy-uzeit.
    MODIFY ztint_spno_log FROM ls_spnolog.
    CLEAR ls_spnolog.
    "解锁
    CALL FUNCTION 'DEQUEUE_EZTINT_SPNO_LOG'
      EXPORTING
        mode_ztint_spno_log = 'E'
        mandt               = sy-mandt
        instanceid          = ls_spnolog-instanceid.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Private Method ZCL_CORPWEIXIN_APPROVAL_API->SAVE_API_LOG
* +-------------------------------------------------------------------------------------------------+
* | [--->] EV_MSG                         TYPE        BAPIRET2(optional)
* | [--->] IV_COMMIT                      TYPE        CHAR01(optional)
* | [--->] IV_KEYVALUE                    TYPE        STRING(optional)
* | [--->] IV_REQUESTBODY                 TYPE        STRING(optional)
* | [--->] IV_RESPONSEBODY                TYPE        STRING(optional)
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD SAVE_API_LOG.
    DATA:ls_apilog TYPE zticec_api_log.
    TRY .
        ls_apilog-apiguid =    cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
      CATCH cx_uuid_error.
    ENDTRY.
    ls_apilog-keyvalue = iv_keyvalue.
    ls_apilog-msgtype = ls_apilog-msgtype.
    ls_apilog-msgid = ls_apilog-msgid.
    ls_apilog-msg = ls_apilog-msg.
    ls_apilog-zcrt_bdate = sy-datum.
    ls_apilog-zcrt_btime = sy-uzeit.
    ls_apilog-zcrt_uname = sy-uname.
    ls_apilog-requestbody = iv_requestbody.
    ls_apilog-responsebody = iv_responsebody.
*    MODIFY zticec_api_log FROM ls_apilog.
    IF iv_commit IS NOT INITIAL..
      COMMIT WORK AND WAIT .
    ENDIF.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->SAVE_BASIC_INFO
* +-------------------------------------------------------------------------------------------------+
* | [--->] IS_DETAIL                      TYPE        TS_APPROVALDETAIL(optional)
* | [<---] ES_HWOBS                       TYPE        ZTFI_HWOBS_LOG
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD save_basic_info.

    DATA:lv_timestamp     TYPE p.
    TRY .
        es_hwobs-guid =   cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
      CATCH cx_uuid_error.
    ENDTRY.

    es_hwobs-num = '1' .
    es_hwobs-processinstanceid = is_detail-info-sp_no. "审批流程ID
    es_hwobs-processcode       = is_detail-info-template_id.
    es_hwobs-title             = is_detail-info-sp_name.

    lv_timestamp  = is_detail-info-apply_time."创建时间
    IF lv_timestamp > 0. "2020-08-22 18:33:50
      zcl_cassint_formatter=>convert_unix_timestamp_to_abap(
        EXPORTING unixsecs = lv_timestamp
        IMPORTING
          ev_date = es_hwobs-applydate
          ev_time = es_hwobs-applytime ).

    ENDIF.
    CLEAR lv_timestamp.

    DATA(lv_notes) = lines( is_detail-info-sp_record )."多少审批节点

    DATA(lt_time) = is_detail-info-sp_record[ lv_notes ]-details.
    SORT lt_time BY sptime DESCENDING.

    READ TABLE lt_time INTO DATA(ls_time) INDEX 1.
    lv_timestamp = ls_time-sptime.
    IF lv_timestamp > 0.
      zcl_cassint_formatter=>convert_unix_timestamp_to_abap(
        EXPORTING unixsecs = lv_timestamp
        IMPORTING ev_date = es_hwobs-finishdate
                  ev_time = es_hwobs-finishtime ).

    ENDIF.
    CLEAR lv_timestamp.
    es_hwobs-dinguserid = is_detail-info-applyer-userid. "申请人ID zhiyong.wu

    SELECT SINGLE userid,username
      INTO (@es_hwobs-userid,@es_hwobs-applyername)
      FROM ztint_user_inf
      WHERE dinguserid = @es_hwobs-dinguserid ."

    es_hwobs-ifobssend = 'X' . "发送之前记录
    es_hwobs-zcrt_uname = sy-uname.
    es_hwobs-zcrt_bdate = sy-datum.
    es_hwobs-zcrt_btime = sy-uzeit.
    MODIFY ztfi_hwobs_log FROM es_hwobs .
    COMMIT WORK .

  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->SEND_DPINFO_TO_ICEC
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD send_dpinfo_to_icec.
    DATA ls_costinf TYPE zcl_icec_merchant_api=>ty_cost_data.
    DATA lo_ding TYPE REF TO zcl_dingding_api.
    DATA lv_partsname(255) TYPE c .
    DATA lo_pi TYPE REF TO zco_si_sap_icec_ding_dpresult.
    DATA ls_output TYPE zmt_icec_ding_dpresult_request.
    DATA ls_formitem LIKE LINE OF  ls_output-mt_icec_ding_dpresult_request-form_items.
    DATA ls_ztint_dp_icec_l TYPE ztint_dp_icec_l.
    DATA lv_orderid TYPE zticec_order_h-orderid..
    DATA lv_amount TYPE ztint_afclaim-price.
    DATA lv_recovermoney TYPE ztint_afclaim-price.
    DATA lv_body    TYPE string.
    DATA lv_subbody TYPE string.
    DATA lv_text(255) TYPE c.
    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
    DATA(lv_code) = ls_detail-info-template_id.
    DATA lv_alert_content TYPE string.
    DATA lv_fname         TYPE string.
    DATA lv_url           TYPE string.
    DATA lv_title         TYPE string.
    DATA lv_msg_type      TYPE string.
    DATA lv_content       TYPE string.
    DATA lv_date_s        TYPE string.
    DATA  lv_message      TYPE string.
    DATA ls_log TYPE ztint_wo_log.
    DATA: lv_timestamp TYPE p.

    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
                           sendmsg = 'X'  ).
      RETURN.
    ENDIF.

    "取是否有配置订单推送起始日
    SELECT SINGLE process_code,orderdate_from INTO @DATA(ls_iceccf) FROM ztint_dp_icec_cf
      WHERE qywx_processcode  = @lv_code.
    SELECT SINGLE *  INTO @DATA(ls_code) FROM ztint_dp_code WHERE processcode = @lv_code.
    "取配置的流程字段
    SELECT * FROM ztint_dp_field INTO TABLE @DATA(lt_ztint_dp_field)
      WHERE processcode = @lv_code.
    SORT lt_ztint_dp_field BY dingname.
    IF ls_iceccf-orderdate_from IS NOT INITIAL.
      "取审批中配置的订单字段
      SELECT SINGLE dingname INTO @DATA(lv_dingname) FROM ztint_dp_field
        WHERE processcode = @lv_code AND name = 'orderid'.
    ENDIF.

*    lv_body = |\{"id":"{ iv_spno }","processCode":"{ ls_iceccf-process_code }"|.
*    ls_output-mt_icec_ding_dpresult_request-id = iv_spno.
*    ls_output-mt_icec_ding_dpresult_request-process_code = ls_iceccf-process_code."钉钉CODE

    LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_form).
      CLEAR ls_formitem.
      ls_formitem-name = VALUE #( ls_form-title[ 1 ]-text OPTIONAL ).
      CASE ls_form-control.
        WHEN 'Text' OR 'Textarea'.
          lv_text = ls_form-value-text.
          CONDENSE lv_text.
          ls_formitem-value = lv_text.
        WHEN 'Number'.
          ls_formitem-value = ls_form-value-new_number.
        WHEN 'Money'.
          ls_formitem-value = ls_form-value-new_money.
        WHEN 'Selector'.
          ls_formitem-value = VALUE #( ls_form-value-selector-options[ 1 ]-value[ 1 ]-text OPTIONAL ).
        WHEN  OTHERS.
      ENDCASE.
*      lv_subbody = COND #( WHEN lv_subbody IS INITIAL THEN |"formItems":[\{"name":"{ ls_formitem-name }","value":"{ ls_formitem-value }"\}|
*                           ELSE |{ lv_subbody },\{"name":"{ ls_formitem-name }","value":"{ ls_formitem-value }"\}| ).
**      APPEND ls_formitem TO ls_output-mt_icec_ding_dpresult_request-form_items.
*      "查找订单id
*      IF ls_formitem-name = lv_dingname.
*        lv_orderid = ls_formitem-value.
*      ENDIF.
      READ TABLE lt_ztint_dp_field INTO DATA(ls_ztint_dp_field) WITH KEY dingname = ls_formitem-name BINARY SEARCH.
      IF sy-subrc = 0.
        CASE ls_ztint_dp_field-name.
          WHEN 'orderid'. "订单编号
            lv_orderid = ls_formitem-value.
          WHEN 'number'."补偿金额
*            IF ls_code-fname = 'CUSPAYA'.
            lv_amount = ls_formitem-value.
*            ENDIF.
          WHEN 'abnormalstage'."异常阶段
            DATA(lv_abnormalstage) = ls_formitem-value.
          WHEN 'providereason'."补偿原因
            DATA(lv_providereason) = ls_formitem-value.
          WHEN 'provideway'."补偿方式
            DATA(lv_provideway) = ls_formitem-value.
          WHEN 'parts'."配件信息
            lv_partsname = ls_formitem-value.
            REPLACE ALL OCCURRENCES OF '/' IN lv_partsname WITH ''.
            REPLACE ALL OCCURRENCES OF '\' IN lv_partsname WITH ''.
            lv_partsname = zcl_cassint_formatter=>filter_emoji_string( EXACT string( lv_partsname ) ).
          WHEN 'claimto'."赔付给谁
            DATA(lv_claimto) = ls_formitem-value.
          WHEN 'recovermoney'."需追商家金额
            lv_recovermoney = ls_formitem-value.
*          WHEN 'claimmoney'."赔付金额
*            lv_amount = ls_formitem-value.
          WHEN OTHERS.
        ENDCASE.
      ENDIF.
      CLEAR ls_ztint_dp_field.
    ENDLOOP.

*    lv_subbody = |{ lv_subbody }]|.
    DATA(lv_notes) = lines( ls_detail-info-sp_record )."多少审批节点
    DATA(lv_time) = lines( ls_detail-info-sp_record[ lv_notes ]-details )."每个审批节点几个人依次审批
    "获取最后一个节点审批人记录
    DATA(lt_record_detail) = VALUE #( ls_detail-info-sp_record[ lv_notes ]-details OPTIONAL ).
    SORT lt_record_detail BY sptime DESCENDING.
    READ TABLE lt_record_detail INTO DATA(ls_record_detail) INDEX 1.
    lv_timestamp = ls_record_detail-sptime.
    IF lv_timestamp > 0.
      zcl_cassint_formatter=>convert_unix_timestamp_to_abap(
        EXPORTING unixsecs = lv_timestamp
        IMPORTING ev_date = DATA(lv_finishdate) ev_time = DATA(lv_finishtime) ).
      CLEAR lv_timestamp.
      lv_timestamp = zcl_cassint_formatter=>convert_abap_to_timestamp(
         date = lv_finishdate time = lv_finishtime ).
      CLEAR:lv_finishtime,lv_finishtime.
    ENDIF.

    "判断是否需要入库账单
    TRY .
        DATA(lv_guid) = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
      CATCH cx_uuid_error.
    ENDTRY.

    IF ls_code-fname = 'CUSPAYA'."异常采购
      IF   lv_providereason = '报价错误' OR
           lv_providereason = '未按约定时间发货' OR
           lv_providereason = '发错地址' OR
           lv_providereason = '漏发/错发货' OR
           lv_providereason = '未贴开思标签' OR
           lv_providereason = '下单后无货'.
        IF lv_amount IS NOT INITIAL.
          lv_amount = COND #( WHEN lv_amount > 0 THEN -1 * lv_amount ELSE lv_amount ).
          ls_costinf = VALUE #( id = iv_spno agreetime = lv_timestamp approvaltype = 'PURCHASE_COMPENSATION'
                                orderid = lv_orderid amount = lv_amount partsname = lv_partsname
                                compensationreason = lv_providereason ).
        ELSE.
          lv_message = |异常采购申请补偿金额为0时，无需进行账单入库|.
        ENDIF.
      ELSE.
        lv_message = |补偿原因为：{ lv_providereason }时，无需进行账单入库|.
      ENDIF.
    ELSEIF ls_code-fname = 'AFTERSALECLAIMOC'."售后赔付申请
      IF lv_claimto EQ '商家' AND lv_provideway NE '现金(需开票)'  AND lv_amount IS NOT INITIAL.
        ls_costinf = VALUE #( id = iv_spno agreetime = lv_timestamp approvaltype = 'AFTER_SALE_COMPENSATION'
                             orderid = lv_orderid amount = lv_amount partsname = lv_partsname
                                compensationreason = lv_providereason ).

      ELSEIF ( lv_claimto EQ '开思' OR lv_claimto EQ '客户' ) AND lv_recovermoney IS NOT INITIAL.
        ls_costinf = VALUE #( id = iv_spno agreetime = lv_timestamp approvaltype = 'AFTER_SALE_COMPENSATION_CASS'
                              orderid = lv_orderid amount = lv_recovermoney * ( -1 ) partsname = lv_partsname
                                compensationreason = lv_providereason  ).
      ELSE.
        lv_message = COND #( WHEN lv_claimto EQ '商家' AND lv_provideway EQ '现金(需开票)'
                             THEN '售后赔付给商家选择现金赔付无需进行账单入库'
                             WHEN lv_claimto EQ '商家' AND lv_amount IS INITIAL
                             THEN '售后赔付给商家时赔付金额为0无需进行账单入库'
                             WHEN ( lv_claimto EQ '开思' OR lv_claimto EQ '客户' ) AND lv_recovermoney IS INITIAL
                             THEN '售后赔付给开思应追商家金额为0无需进行账单入库' ).
      ENDIF.
    ENDIF.

    IF ls_costinf IS NOT INITIAL.
      DATA(lo_bill) = NEW zcl_icec_billcommon_api( ).
      DATA(ls_return) = lo_bill->set_cost_detail( ls_costinf ).
      IF ls_return-code EQ '200'.
        ls_log = VALUE #( guid = lv_guid event_id = 'DPCREATEBILL' event_desc = '审批流通过账单入库'
                          status = 'S' message = '账单入库成功' orderno = iv_spno
                          key_value1 = lv_orderid key_value2 = ls_code-fname key_value3 = ls_costinf-amount
                          key_value4 = lv_providereason key_value5 = |实例号：{ iv_spno },流程code{ lv_code }|
                          zcrt_bdate = sy-datum zcrt_btime = sy-uzeit ).
      ELSE."推送内部人员处理
        lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
        lv_alert_content = '开思助手 \n '   && |有{ ls_code-fnamedesc }审批流账单入库失败,请关注! \n|
                             && '失败原因：' && ls_return-errormessage && ' \n '
                             && '实例号：' && iv_spno && ' \n '
                             && '流程code：' && lv_code && ' \n '
                             &&  '时间：' && lv_date_s && ' \n '
                             &&  '备注:ZTINT_WO_LOG(EVENT_ID=DPCREATEBILL)查看日志ZTINT_CUSPEP_H查看申请记录' .
        lv_fname = 'DPCREATEBILL'.
        lv_title =  '开思助手'.
        lv_msg_type = 'text'.
        CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
          EXPORTING
            iv_content = lv_alert_content
            iv_fname   = lv_fname.
        ls_log = VALUE #( guid = lv_guid event_id = 'DPCREATEBILL' event_desc = '审批流通过账单入库'
                  status = 'E' message = |账单入库失败，原因{ ls_return-errormessage }| orderno = iv_spno
                  key_value1 = lv_orderid key_value2 = ls_code-fname key_value3 = ls_costinf-amount
                  key_value4 = lv_providereason key_value5 = |实例号：{ iv_spno },流程code{ lv_code }|
                  zcrt_bdate = sy-datum zcrt_btime = sy-uzeit ).
      ENDIF.
    ELSE.
      ls_log = VALUE #( guid = lv_guid event_id = 'DPCREATEBILL' event_desc = '审批流通过账单入库'
                    status = '' message = lv_message orderno = iv_spno
                    key_value1 = lv_orderid key_value2 = ls_code-fname key_value3 = lv_amount
                    key_value4 = lv_providereason key_value5 = |实例号：{ iv_spno },流程code{ lv_code }|
                    zcrt_bdate = sy-datum zcrt_btime = sy-uzeit ).
    ENDIF.

    CLEAR ls_ztint_dp_icec_l.
    ls_ztint_dp_icec_l-instanceid = iv_spno.
    ls_ztint_dp_icec_l-processcode = lv_code.
    ls_ztint_dp_icec_l-key_value1 = lv_orderid.
    ls_ztint_dp_icec_l-zupd_bdate = sy-datum.
    ls_ztint_dp_icec_l-zupd_btime = sy-uzeit.
    ls_ztint_dp_icec_l-zupd_uname = sy-uname.
    MODIFY ztint_dp_icec_l FROM ls_ztint_dp_icec_l.

    IF  ls_log IS  NOT INITIAL.
      MODIFY ztint_wo_log FROM ls_log.
    ENDIF.


  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->SEND_ICEC_CUSTOMER_COIN
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD send_icec_customer_coin.
    DATA lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l.
    DATA ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
    DATA:ls_ztint_dp_coin_l TYPE ztint_dp_coin_l.
    DATA:lt_ztint_dp_coin_h TYPE STANDARD TABLE OF ztint_dp_coin_h,
         ls_ztint_dp_coin_h LIKE LINE OF lt_ztint_dp_coin_h,
         lt_ztint_dp_coin_i TYPE STANDARD TABLE OF ztint_dp_coin_i,
         ls_ztint_dp_coin_i LIKE LINE OF lt_ztint_dp_coin_i.

    DATA:lt_user_content TYPE  zsint_push_userlist_tab,
         ls_user_content TYPE  zsint_push_userlist.
    DATA:ls_list     TYPE zcl_icec_coupon_api=>ty_send_goldcoin,
         lt_goldcoin TYPE STANDARD TABLE OF zcl_icec_coupon_api=>ty_send_goldcoin.
    DATA:lv_body          TYPE string,
         lv_timestamp     TYPE p,
         lv_instance      TYPE zde_intprocessinstanceid,
         lv_alert_content TYPE string,
         lv_fname         TYPE string,
         lv_url           TYPE string,
         lv_title         TYPE string,
         lv_msg_type      TYPE string,
         lv_content       TYPE string,
         lv_date_s        TYPE string,
         lv_companyname   TYPE string,
         lv_provideway    TYPE string.
    DATA:lt_msglist TYPE STANDARD TABLE OF ztint_msg_list,
         ls_msglist TYPE ztint_msg_list.

    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
    DATA(lv_code) = ls_detail-info-template_id.

    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
                           sendmsg = 'X' fname = 'BULELIST' ).
      RETURN.
    ENDIF.

    lv_instance = iv_spno.

    DATA lv_status TYPE string.
    lv_status = COND #( WHEN iv_type EQ 'agree' THEN 'APPROVE'
                        WHEN iv_type EQ 'refuse' THEN  'UNAPPROVE'
                        WHEN iv_type EQ 'terminate' THEN 'CANCELED' ELSE '' ).
    "查询审批流是否有对应的工单
    SELECT SINGLE woid INTO @DATA(lv_woid) FROM ztint_afclaim WHERE businessid = @lv_instance.
    "审批结果
    CASE iv_type.
      WHEN 'agree'. "审批通过

        SELECT SINGLE * FROM ztint_dp_coin_h
          INTO CORRESPONDING FIELDS OF @ls_ztint_dp_coin_h
          WHERE processinstanceid =  @iv_spno.
        IF ls_ztint_dp_coin_h-sendstatus = 'Y'. "已发放
          "已发放记录日志
          TRY .
              ls_ztint_dp_coin_l-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
              ls_ztint_dp_coin_l-event_desc = '实例发放金币'.
              ls_ztint_dp_coin_l-event_id = 'send_coin_already'.
              ls_ztint_dp_coin_l-status = 'E'.
              ls_ztint_dp_coin_l-message = '实例已发放过金币'.
              ls_ztint_dp_coin_l-key_value1 = iv_spno.
              ls_ztint_dp_coin_l-key_value2 = lv_code.
              ls_ztint_dp_coin_l-zcrt_bdate = sy-datum.
              ls_ztint_dp_coin_l-zcrt_btime = sy-uzeit.
              MODIFY ztint_dp_coin_l FROM ls_ztint_dp_coin_l.
            CATCH cx_uuid_error.
          ENDTRY.
          RETURN.
        ENDIF.

        "
        SELECT SINGLE * FROM ztint_dp_code INTO @DATA(ls_ztint_dp_code) WHERE processcode = @lv_code.
        "取配置的流程字段
        SELECT * FROM ztint_dp_field INTO TABLE @DATA(lt_ztint_dp_field) WHERE processcode = @lv_code.
        SORT lt_ztint_dp_field BY dingname.
        "取配置的流程字段值
        SELECT * FROM ztint_dp_value INTO TABLE @DATA(lt_ztint_dp_value) WHERE processcode = @lv_code.



        "锁表
        CALL FUNCTION 'ENQUEUE_EZ_INT_DP_COIN_H'
          EXPORTING
            mode_ztint_dp_coin_h = 'E'
            mandt                = sy-mandt
            instanceid           = lv_instance.
        IF sy-subrc <> 0.
          "锁表失败 记录日志
          TRY .
              ls_ztint_dp_coin_l-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
              ls_ztint_dp_coin_l-event_desc = '发放金币锁表失败'.
              ls_ztint_dp_coin_l-event_id = 'lock_ztint_coin_h_fail'.
              ls_ztint_dp_coin_l-status = 'E'.
              ls_ztint_dp_coin_l-message = '实例发放金币锁表失败'.
              ls_ztint_dp_coin_l-key_value1 = iv_spno.
              ls_ztint_dp_coin_l-key_value2 = lv_code.
              ls_ztint_dp_coin_l-zcrt_bdate = sy-datum.
              ls_ztint_dp_coin_l-zcrt_btime = sy-uzeit.
              MODIFY ztint_dp_coin_l FROM ls_ztint_dp_coin_l.
            CATCH cx_uuid_error.
          ENDTRY.
          RETURN.
        ENDIF.

        "没发过
        "抬头信息
        ls_ztint_dp_coin_h-processinstanceid = ls_detail-info-sp_no.
        ls_ztint_dp_coin_h-processcode = ls_detail-info-template_id.
        ls_ztint_dp_coin_h-title = ls_detail-info-sp_name.
*    ls_ztint_dp_coin_h-businessid = ls_detail-business_id.
        ls_ztint_dp_coin_h-zresult = iv_type.

        lv_timestamp  = ls_detail-info-apply_time."创建时间
        IF lv_timestamp > 0.
          zcl_cassint_formatter=>convert_unix_timestamp_to_abap(
            EXPORTING unixsecs = lv_timestamp
            IMPORTING ev_date = ls_ztint_dp_coin_h-createdate ev_time = ls_ztint_dp_coin_h-createtime ).
          ls_ztint_dp_coin_h-createstamp = lv_timestamp.
          CONDENSE ls_ztint_dp_coin_h-createstamp.
        ENDIF.
        CLEAR lv_timestamp.

        DATA(lv_notes) = lines( ls_detail-info-sp_record )."多少审批节点
*        DATA(lv_time) = lines( ls_detail-info-sp_record[ lv_notes ]-details )."每个审批节点几个人依次审批
*        lv_timestamp = VALUE #( ls_detail-info-sp_record[ lv_notes ]-details[ lv_time ]-sptime OPTIONAL ).
        DATA(lt_time) = ls_detail-info-sp_record[ lv_notes ]-details.
        SORT lt_time BY sptime DESCENDING.
        READ TABLE lt_time INTO DATA(ls_time) INDEX 1.
        lv_timestamp = ls_time-sptime.
        IF lv_timestamp > 0.
          zcl_cassint_formatter=>convert_unix_timestamp_to_abap(
            EXPORTING unixsecs = lv_timestamp
            IMPORTING ev_date = ls_ztint_dp_coin_h-finishdate ev_time = ls_ztint_dp_coin_h-finishtime ).
          ls_ztint_dp_coin_h-finishstamp = lv_timestamp.
          CONDENSE ls_ztint_dp_coin_h-finishstamp.
        ENDIF.
        CLEAR lv_timestamp.
        ls_ztint_dp_coin_h-status = 'finish'.
        ls_ztint_dp_coin_h-originatordeptid = ls_detail-info-applyer-partyid.
        ls_ztint_dp_coin_h-originatoruserid = ls_detail-info-applyer-userid.

        "审批详情
        LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
          CLEAR ls_ztint_dp_coin_i.
          ls_ztint_dp_coin_i-processinstanceid = iv_spno.
          ls_ztint_dp_coin_i-posnr = sy-tabix.
          ls_ztint_dp_coin_i-name = VALUE #( ls_content-title[ 1 ]-text OPTIONAL ).
          CASE ls_content-control.
            WHEN 'Text' OR 'Textarea'.
              ls_ztint_dp_coin_i-value = ls_content-value-text.
            WHEN 'Number'.
              ls_ztint_dp_coin_i-value = ls_content-value-new_number.
            WHEN 'Money'.
              ls_ztint_dp_coin_i-value = ls_content-value-new_money.
            WHEN 'Selector'.
              ls_ztint_dp_coin_i-value = VALUE #( ls_content-value-selector-options[ 1 ]-value[ 1 ]-text OPTIONAL ).
            WHEN  OTHERS.
          ENDCASE.
          APPEND ls_ztint_dp_coin_i TO lt_ztint_dp_coin_i.

          "准备调用接口数据
          READ TABLE lt_ztint_dp_field INTO DATA(ls_ztint_dp_field) WITH  KEY dingname = ls_ztint_dp_coin_i-name BINARY SEARCH.
          IF sy-subrc = 0.
            CASE  ls_ztint_dp_field-name.
              WHEN 'companycode'. "公司code
                "转大写  去空格
                ls_list-companycode = ls_ztint_dp_coin_i-value.
                CONDENSE ls_list-companycode NO-GAPS .
                TRANSLATE ls_list-companycode TO UPPER CASE.
                SELECT SINGLE companyid INTO ls_list-companyid FROM ztint_cus_inf WHERE companycode = ls_list-companycode.
              WHEN 'number' ."发放数量
                ls_list-number = ls_ztint_dp_coin_i-value.
              WHEN 'orderid'."订单号
                ls_list-orderid = ls_ztint_dp_coin_i-value.
              WHEN 'providereason'. "发放补充原因
                ls_list-providereason = ls_ztint_dp_coin_i-value.
                REPLACE ALL OCCURRENCES OF '\'  IN ls_list-providereason WITH ''.
              WHEN 'remark'."备注 内部人员查看
                ls_list-remark = zcl_cassint_formatter=>filter_emoji_string( EXACT string( ls_ztint_dp_coin_i-value ) ).
**                ls_list-remark = ls_ztint_dp_coin_i-value.
**                REPLACE ALL OCCURRENCES OF '\'  IN ls_list-remark WITH ''.
              WHEN 'companyname'."维修厂名称
                lv_companyname = ls_ztint_dp_coin_i-value.
              WHEN 'provideway'."赔付方式
                lv_provideway = ls_ztint_dp_coin_i-value.
                DATA(lv_flag) = 'X'.
              WHEN 'claimto'."赔付给
                DATA(lv_payto) = ls_ztint_dp_coin_i-value.
              WHEN OTHERS.
            ENDCASE.
          ENDIF.
          CLEAR ls_ztint_dp_field.
        ENDLOOP.

        IF ( lv_provideway NE '金币' AND lv_flag EQ 'X' )
           OR lv_payto EQ '开思' or lv_payto eq '商家'."赔付给开思就不要赔金币了....
          "记录日志
          TRY .
              ls_ztint_dp_coin_l-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
            CATCH cx_uuid_error.

          ENDTRY.
          ls_ztint_dp_coin_l-event_desc = '实例发放金币'.
          ls_ztint_dp_coin_l-event_id = 'not_coin'.
          ls_ztint_dp_coin_l-status = 'E'.
          ls_ztint_dp_coin_l-message = '补偿方式不是金币'.
          ls_ztint_dp_coin_l-key_value1 = iv_spno.
          ls_ztint_dp_coin_l-key_value2 = lv_code.
          ls_ztint_dp_coin_l-zcrt_bdate = sy-datum.
          ls_ztint_dp_coin_l-zcrt_btime = sy-uzeit.
          MODIFY ztint_dp_coin_l FROM ls_ztint_dp_coin_l.
          RETURN.
        ENDIF.

        IF lv_woid IS NOT INITIAL."如果工单已经关闭，就不能发放金币
          SELECT SINGLE wostatus INTO @DATA(lv_wostatus) FROM ztint_wo_header WHERE woid = @lv_woid.
          IF sy-subrc EQ 0 AND ( lv_wostatus EQ '2' OR lv_wostatus EQ '3').
            "已发放记录日志
            TRY .
                ls_ztint_dp_coin_l-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
                ls_ztint_dp_coin_l-event_desc = '实例发放金币'.
                ls_ztint_dp_coin_l-event_id = 'send_coin_already'.
                ls_ztint_dp_coin_l-status = 'E'.
                ls_ztint_dp_coin_l-message = '工单已关闭'.
                ls_ztint_dp_coin_l-key_value1 = iv_spno.
                ls_ztint_dp_coin_l-key_value2 = lv_code.
                ls_ztint_dp_coin_l-zcrt_bdate = sy-datum.
                ls_ztint_dp_coin_l-zcrt_btime = sy-uzeit.
                MODIFY ztint_dp_coin_l FROM ls_ztint_dp_coin_l.

                UPDATE ztint_wo_note SET afclaimstaus = 'APPROVAL'
                                         zupd_bdate = sy-datum zupd_btime = sy-uzeit zupd_uname = sy-uname
                                   WHERE processcode = lv_instance.
                RETURN.
              CATCH cx_uuid_error.
            ENDTRY.
            RETURN.
          ENDIF.
        ENDIF.


        "特殊发放原因 默认一个的
        IF ls_list-providereason IS INITIAL.
          SELECT SINGLE value INTO ls_list-providereason FROM ztint_dp_value
            WHERE processcode = lv_code AND name = 'providereason'.
        ENDIF.
        "获取发起人名称
        SELECT SINGLE username INTO @DATA(lv_username) FROM ztint_user_inf
          WHERE qywxuserid = @ls_detail-info-applyer-userid.
        IF lv_username IS INITIAL.
          lv_username = '企业微信'.
        ENDIF.
        ls_list-createdby = lv_username.
        ls_list-approveflowid =  COND #( WHEN ls_list-providereason EQ '严选赔付' THEN ls_ztint_dp_code-processcode
                                         ELSE ls_ztint_dp_code-dingprocesscode ).
        APPEND ls_list TO lt_goldcoin.
        DATA(lo_coupon) = NEW zcl_icec_coupon_api( ).
        DO 3 TIMES.
          lo_coupon->send_customer_coin( EXPORTING it_goldcoin = lt_goldcoin
                                         IMPORTING ot_result = DATA(lt_rerult) es_msg = DATA(ls_msg) ).
          READ TABLE lt_rerult INTO DATA(ls_rerult) INDEX 1.
          IF ls_rerult-success  EQ 'true' OR ls_rerult-success EQ 'X'.
            EXIT.
          ENDIF.
          CLEAR:ls_rerult,lt_rerult.
        ENDDO.

        IF ls_rerult-success EQ 'true' OR ls_rerult-success EQ 'X'.
          DATA lv_sendcoinstatus TYPE char10.
          lv_sendcoinstatus = 'S'.
          "发放金币成功
          ls_ztint_dp_coin_h-sendstatus = 'Y'. "没发放
          ls_ztint_dp_coin_h-senddate = sy-datum.
          ls_ztint_dp_coin_h-sendtime = sy-uzeit.
          ls_ztint_dp_coin_h-sendmsg = '发放成功'.

          "记录日志
          TRY .
              ls_ztint_dp_coin_l-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
              ls_ztint_dp_coin_l-event_desc = '发放金币成功'.
              ls_ztint_dp_coin_l-event_id = 'send_coin_success'.
              ls_ztint_dp_coin_l-status = 'S'.
              ls_ztint_dp_coin_l-message = '发放金币成功'.
              ls_ztint_dp_coin_l-key_value1 = iv_spno.
              ls_ztint_dp_coin_l-key_value2 = lv_code.
              ls_ztint_dp_coin_l-zcrt_bdate = sy-datum.
              ls_ztint_dp_coin_l-zcrt_btime = sy-uzeit.
              MODIFY ztint_dp_coin_l FROM ls_ztint_dp_coin_l.
            CATCH cx_uuid_error.
          ENDTRY.

          "金币发放成功，更新工单对应的审批流的状态
          IF lv_woid IS NOT INITIAL.
            SELECT SINGLE afclaimstaus INTO @DATA(lv_afclaimstatus) FROM ztint_wo_note WHERE woid = @lv_woid AND processcode = @lv_instance.
            IF sy-subrc EQ 0 AND ( lv_afclaimstatus IS INITIAL OR lv_afclaimstatus EQ 'SUBMIT' ).
              UPDATE ztint_wo_note SET afclaimstaus = 'APPROVAL'
                                   zupd_bdate = sy-datum zupd_btime = sy-uzeit zupd_uname = sy-uname
                                   WHERE processcode = lv_instance.
              "更新工单中心质保工单流程
              SELECT SINGLE * INTO @DATA(ls_afclaim) FROM ztint_afclaim WHERE instanceid = @lv_instance .
              DATA(lo_wo) = NEW zcl_zgs_icerp_cassi_16_dpc_ext( ).
              lo_wo->afclaim_approval_process( woid = ls_afclaim-woid instanceid = ls_afclaim-instanceid afclaimstaus = EXACT string( lv_status )
                                              sendcoinstatus = lv_sendcoinstatus ).
            ENDIF.
          ENDIF.

          "售后赔付申请通知相应的空军待办
          SELECT SINGLE companyid,companycode,companyname,orderid FROM ztint_afclaim INTO @DATA(ls_afclaim_order) WHERE instanceid = @lv_instance.
          IF ls_afclaim_order-orderid IS NOT INITIAL.
            SELECT SINGLE u~userid FROM ztint_cus_inf AS i INNER JOIN ztint_cus_user AS u
              ON i~cusid = u~cusid AND u~usertype = '2' AND u~userchildtype = 'A' AND u~ispre = '' AND u~isdelete = ''
              INNER JOIN ztint_user_inf AS ui ON u~userid = ui~userid AND ui~isstill = 'X'
              INTO @DATA(lv_airuser) WHERE companyid = @ls_afclaim_order-companyid.
            IF sy-subrc EQ 0.
              TRY.
                  DATA(lv_messageid) = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
                  lt_msglist = VALUE #( BASE lt_msglist ( messageid = lv_messageid
                                msgfirstcat = 'ORDER' msgsecondcat = 'AFCLAIM'   "订单-订单赔付
                                touserid = lv_airuser senddate = sy-datum sendtime = sy-uzeit msgstatus = '0'
                                msgkeyvalue = ls_afclaim_order-orderid msgkeyvalue_e = |{ ls_afclaim_order-companycode }|
                                msgtitle = |客户【{ ls_afclaim_order-companyname }】的订单{ ls_afclaim_order-orderid }金币已赔付|
                                msgcontent = |客户【{ ls_afclaim_order-companyname }】的订单{ ls_afclaim_order-orderid }金币已赔付，| &&
                                |赔付理由：{ ls_list-providereason }，赔付金币：{ ls_list-number },备注：{ ls_list-remark }|
                                appurlkey = ls_afclaim_order-orderid
                                zcrt_bdate = sy-datum zcrt_btime = sy-uzeit zupd_bdate = sy-datum zupd_btime = sy-uzeit ) ).
                CATCH cx_uuid_error INTO DATA(lcx_nofound).
              ENDTRY.
              IF lt_msglist IS NOT INITIAL.
                CALL FUNCTION 'Z_FMINT_SAVE_MSGLIST_AYN'
                  TABLES
                    it_msglist = lt_msglist.
              ENDIF.
            ENDIF.
          ENDIF.

          "推送提醒
          IF ls_ztint_dp_coin_h-originatoruserid IS NOT INITIAL.
            "通知申请人额度生效
            CLEAR lv_date_s.
            CLEAR lv_content.
            lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
            lv_content =   '开思助手 \n '   && ls_ztint_dp_coin_h-title && '金币已发放，请知悉！ \n '
             && '审批编码:' && ls_ztint_dp_coin_h-processinstanceid && ' \n '
             && '客户名称:' && lv_companyname && ' \n '
             && '客户代码:' && ls_list-companycode && ' \n '
             && '金币:' && ls_list-number  &&  ' \n '
             && lv_date_s.

            CLEAR ls_user_content.
            ls_user_content-content = lv_content.
            ls_user_content-userid = ls_ztint_dp_coin_h-originatoruserid.
            ls_user_content-orderno = |{ ls_list-companycode }|.
            ls_user_content-orderdesc = |{ lv_companyname }|.
            ls_user_content-firstcatgory = '客户管理'.
            ls_user_content-secondcatgory = '客户金币发放申请审批'.
            APPEND ls_user_content TO lt_user_content.
          ENDIF.
        ELSE.
          lv_sendcoinstatus = 'N'.

          "发放金币失败
          ls_ztint_dp_coin_h-sendstatus = 'N'. "没发放
          ls_ztint_dp_coin_h-senddate = sy-datum.
          ls_ztint_dp_coin_h-sendtime = sy-uzeit.
          IF ls_rerult-remark IS INITIAL.
            ls_rerult-remark = '发放失败'.
          ENDIF.

          ls_ztint_dp_coin_h-sendmsg = ls_rerult-remark.
          "记录日志
          "记录日志
          TRY .
              ls_ztint_dp_coin_l-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
              ls_ztint_dp_coin_l-event_desc = '发放金币失败'.
              ls_ztint_dp_coin_l-event_id = 'send_coin_fail'.
              ls_ztint_dp_coin_l-status = 'E'.
              ls_ztint_dp_coin_l-message = '发放金币失败'.
              ls_ztint_dp_coin_l-key_value1 = iv_spno.
              ls_ztint_dp_coin_l-key_value2 = lv_code.
              ls_ztint_dp_coin_l-zcrt_bdate = sy-datum.
              ls_ztint_dp_coin_l-zcrt_btime = sy-uzeit.
              MODIFY ztint_dp_coin_l FROM ls_ztint_dp_coin_l.
            CATCH cx_uuid_error.
          ENDTRY.
        ENDIF.

        "保存信息
        MODIFY ztint_dp_coin_h FROM ls_ztint_dp_coin_h.
        MODIFY ztint_dp_coin_i FROM TABLE lt_ztint_dp_coin_i.

        "锁表
        CALL FUNCTION 'DEQUEUE_EZ_INT_DP_COIN_H'
          EXPORTING
            mode_ztint_dp_coin_h = 'E'
            mandt                = sy-mandt
            instanceid           = lv_instance.
        .

        "推送通知
        IF lt_user_content IS NOT INITIAL.
          lv_title =  '开思助手'.
          lv_msg_type = 'text'.
          CONDENSE lv_url NO-GAPS .

          CALL FUNCTION 'Z_FMQYWX_CASSINT_PUSH_MESSAGE'
            IN UPDATE TASK
            EXPORTING
              iv_title    = lv_title
              iv_msgtype  = lv_msg_type
              iv_url      = lv_url
            TABLES
              it_userlist = lt_user_content.

        ENDIF.

      WHEN 'refuse'."审批拒绝
        "更新工单对应的审批流的状态
        IF lv_woid IS NOT INITIAL.
          UPDATE ztint_wo_note SET afclaimstaus = 'UNAPPROVE'
                                         zupd_bdate = sy-datum zupd_btime = sy-uzeit zupd_uname = sy-uname
                              WHERE processcode = lv_instance.
        ENDIF.
      WHEN 'terminate'."审批撤销
        "更新工单对应的审批流的状态
        IF lv_woid IS NOT INITIAL.
          UPDATE ztint_wo_note SET afclaimstaus = 'CANCELED'
                                   zupd_bdate = sy-datum zupd_btime = sy-uzeit zupd_uname = sy-uname
                             WHERE processcode = lv_instance.
        ENDIF.
      WHEN OTHERS.
    ENDCASE.
    "更新表状态
    SELECT SINGLE guid INTO @DATA(l_guid) FROM ztint_dp_inf WHERE instanceid = @lv_instance.
    CHECK sy-subrc EQ 0.
    "锁表
    CALL FUNCTION 'ENQUEUE_EZ_INT_DP_INF'
      EXPORTING
        mode_ztint_dp_inf = 'E'
        mandt             = sy-mandt
        guid              = l_guid.
    IF sy-subrc <> 0.
      ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'dailycoin_update'
                eventdesc = '日常金币营销申请审批流状态更新' status = 'E' message = '更新审批状态锁表失败'
                key1 = iv_spno key2 = lv_code ).
      MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      RETURN.
    ENDIF.
    SELECT SINGLE * FROM ztint_dailycoin INTO @DATA(ls_dailycoin) WHERE guid = @l_guid.
    IF sy-subrc NE 0.
      UPDATE ztint_dp_inf
         SET status = lv_status
             msg = 'finish'
             zupd_bdate = sy-datum
             zupd_btime = sy-uzeit
             zupd_uname = sy-uname
        WHERE guid = l_guid.
    ELSE.
      UPDATE ztint_dp_inf
           SET status = lv_status
               msg = 'finish'
               zupd_bdate = sy-datum
               zupd_btime = sy-uzeit
               zupd_uname = sy-uname
          WHERE guid = l_guid.
      UPDATE ztint_dailycoin
           SET status = lv_status
               msg = 'finish'
               sendstatus = ls_ztint_dp_coin_h-sendstatus
               sendmsg = ls_ztint_dp_coin_h-sendmsg
               businessid = iv_spno
               zupd_bdate = sy-datum
               zupd_btime = sy-uzeit
               zupd_uname = sy-uname
          WHERE guid = l_guid.
    ENDIF.
    CALL FUNCTION 'DEQUEUE_EZ_INT_DP_INF'
      EXPORTING
        mode_ztint_dp_inf = 'E'
        mandt             = sy-mandt
        guid              = l_guid.


  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->SEND_ICEC_CUSTOMER_COIN_HQ
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* | [--->] IV_CODE                        TYPE        STRING(optional)
* | [--->] IV_NUMBER                      TYPE        INT4(optional)
* | [--->] IV_NUMWITHDECIMAL              TYPE        DMBTR(optional)
* | [--->] IS_DETAIL                      TYPE        TS_APPROVALDETAIL(optional)
* | [<---] IS_DP_PI_H                     TYPE        ZTINT_DP_PI_H
* | [<---] IV_SUCCESSNUM                  TYPE        INT4
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD send_icec_customer_coin_hq.
*接口地址 http://ctsp.casstime.com/interface/manage/detail?id=14428&model_id=24582&classify=2483&method=POST&product_id=1
    DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h, "审批发放抬头
          ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,

          lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i, "审批流的字段值详情
          ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,

          lt_ztint_dp_pi_s TYPE STANDARD TABLE OF ztint_dp_pi_s, "审批发放明细详情（优惠券、金币）-总部
          ls_ztint_dp_pi_s LIKE LINE OF lt_ztint_dp_pi_s,

          lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l, "钉钉审批已审批log
          ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.

    DATA:lt_user_content TYPE  zsint_push_userlist_tab,
         ls_user_content TYPE  zsint_push_userlist.

    DATA:ls_list     TYPE zcl_icec_coupon_api=>ty_send_goldcoin,
         lt_goldcoin TYPE STANDARD TABLE OF zcl_icec_coupon_api=>ty_send_goldcoin.

    DATA:lv_body          TYPE string,
         lv_timestamp     TYPE p,
         lv_instance      TYPE zde_intprocessinstanceid,
         lv_alert_content TYPE string,
         lv_fname         TYPE string,
         lv_url           TYPE string,
         lv_title         TYPE string,
         lv_msg_type      TYPE string,
         lv_content       TYPE string,
         lv_date_s        TYPE string,
         lv_companyname   TYPE string,
         lv_provideway    TYPE string.
    DATA:lt_msglist TYPE STANDARD TABLE OF ztint_msg_list,
         ls_msglist TYPE ztint_msg_list.
    DATA:lv_status TYPE string,
         lv_code   TYPE string.


*审批结果为 agree 发放
    lv_status = COND #( WHEN iv_type EQ 'agree' THEN 'APPROVE'  "同意
                        WHEN iv_type EQ 'refuse' THEN  'UNAPPROVE' "驳回
                        WHEN iv_type EQ 'terminate' THEN 'CANCELED' ELSE '' ). "撤销

*总部营销发金币都是审批完成后实时发放

    IF iv_numwithdecimal IS NOT INITIAL."如果入参传了带小数位的则发放时带小数位
      iv_number = iv_numwithdecimal.
    ENDIF.

    IF is_detail IS INITIAL . "开始不发放，审核通过后发放时，单独调用一次【二期发放使用】
      DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).  "审批流的实例单号202503190002
      lv_code   = ls_detail-info-template_id. "模版code
      IF ls_detail-errcode NE '0'.
        "没找到 记录日志
        log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail_new'
                             zevent = '获取企业微信审批实例'
                             zmsg = '获取企业微信审批实例失败'
                             zvalue1 = iv_spno
                             zvalue2 = lv_code
                             sendmsg = 'X'
                             fname = 'BULELIST' ).
        RETURN.
      ENDIF.
    ELSE.
      ls_detail = is_detail .
      lv_code = iv_code .
    ENDIF.

*-判断主表是否有申请记录
    "获取申请记录
    SELECT SINGLE * FROM ztint_market_req
      INTO @DATA(ls_ztint_req)
      WHERE instanceid = @iv_spno.
    IF sy-subrc NE 0.
      "没找到 记录日志
      ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_coin_hq'
                                                eventdesc = '总部营销-发放金币'
                                                status = 'E'
                                                message = '未找到对应的申请记录'
                                                key1 = iv_spno
                                                key2 = lv_code ).
      MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      RETURN.
    ENDIF.

*判断是否已经全部发放过
    SELECT SINGLE *
      FROM ztint_dp_pi_h
      INTO CORRESPONDING FIELDS OF @ls_ztint_dp_pi_h
      WHERE processinstanceid = @iv_spno.

    IF ls_ztint_dp_pi_h-sendstatus = 'Y'. "已发放过
      ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_coin_hq'
                                                eventdesc = '总部营销-发放金币'
                                                status = 'E'
                                                message = '实例已申请发放过金币'
                                                key1 = iv_spno
                                                key2 = lv_code ).

      MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      RETURN.
    ENDIF.

    lv_instance = iv_spno.
*        SELECT SINGLE * FROM ztint_dp_code
*          INTO @DATA(ls_ztint_dp_code)
*          WHERE processcode = @lv_code.

    "锁表
    CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
      EXPORTING
        mode_ztint_dp_pi_h = 'E'
        mandt              = sy-mandt
        instanceid         = lv_instance.

    IF sy-subrc <> 0.
      "锁表失败 记录日志
      ls_ztint_dp_pi_l   = prepare_ztint_dp_pi_l( eventid   = 'lock_ztint_dp_pi_h'
                                       eventdesc = '总部营销-发放金币锁表失败'
                                       status  = 'E'
                                       message = '实例发放金币锁表失败'
                                       key1 = iv_spno
                                       key2 = lv_code ).

      MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      RETURN.
    ENDIF.

    "没发过——这里第一次写抬头H表
    ls_ztint_dp_pi_h = prepare_ztint_dp_pi_h( is_detail = ls_detail iv_type = iv_type ).

    "审批详情
    LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
      CLEAR ls_ztint_dp_pi_i.
      ls_ztint_dp_pi_i-processinstanceid = iv_spno.  "实例名称
      ls_ztint_dp_pi_i-posnr = sy-tabix.
      ls_ztint_dp_pi_i-name = VALUE #( ls_content-title[ lang = 'zh_CN' ]-text OPTIONAL ). "中文名

      CASE ls_content-control.
        WHEN 'Text' OR 'Textarea'.
          ls_ztint_dp_pi_i-value = ls_content-value-text.
        WHEN 'Number'.
          ls_ztint_dp_pi_i-value = ls_content-value-new_number.
        WHEN 'Money'.
          ls_ztint_dp_pi_i-value = ls_content-value-new_money.
        WHEN 'Selector'.
          ls_ztint_dp_pi_i-value = VALUE #( ls_content-value-selector-options[ 1 ]-value[ 1 ]-text OPTIONAL ).
        WHEN  OTHERS.
      ENDCASE.

      APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.  "审批的每个字段的值
    ENDLOOP.

    "获取发起人名称
    SELECT SINGLE username,userid
       FROM ztint_user_inf
       INTO ( @DATA(lv_username),@DATA(lv_userid) )
      WHERE qywxuserid = @ls_detail-info-applyer-userid.
    IF lv_username IS INITIAL.
      lv_username = '企业微信'.
    ENDIF.

    "发金币要用固定的模版ID
    SELECT SINGLE dingprocesscode
      FROM ztint_dp_code
      INTO @DATA(lv_id)
      WHERE processcode = @lv_code.

*客户清单及金币发放数量
    SELECT *
      FROM ztint_hq_cuslist
      INTO TABLE @DATA(lt_cuslist)
      WHERE guid = @ls_ztint_req-guid.
    SORT lt_cuslist BY companycode .

*-已发放的清单
    SELECT *
      FROM ztint_dp_pi_s
      INTO TABLE @DATA(lt_s)
      WHERE guid = @ls_ztint_req-guid.
    SORT lt_s BY companycode  .

*-整理数据
    LOOP AT lt_cuslist INTO DATA(ls_cus) .
      CLEAR:ls_list.
      READ TABLE lt_s INTO DATA(ls_s) WITH KEY companycode = ls_cus-companycode
                                               BINARY SEARCH.
      IF sy-subrc = 0 AND ls_s-sendstatus = 'Y'."读到并且已发放(总部的金币，要么全发，要么不发)
        CONTINUE.
      ELSE.

        ls_list-companycode = ls_cus-companycode .
        "公司code —>companyid
        SELECT SINGLE companyid
          INTO ls_list-companyid
          FROM ztint_cus_inf WHERE companycode = ls_cus-companycode.

        "发放数量
        ls_list-number = ls_cus-coincount.

        "发放理由——维修厂可见 ，给值营销类别
        SELECT SINGLE mlname INTO @ls_list-providereason
          FROM ztint_mltype
          WHERE mltype = @ls_ztint_req-mltype.

        "申请理由 —— 给值备注 内部人员查看
        ls_list-remark = ls_ztint_req-reqreason .

        "发金币要用固定的ID
        ls_list-approveflowid = lv_id .

        "创建人
        ls_list-createdby = lv_username.

        APPEND ls_list TO lt_goldcoin.
      ENDIF.

      CLEAR:ls_list ,ls_cus.
    ENDLOOP.

    DATA(lo_coupon) = NEW zcl_icec_coupon_api( ).
    DO 3 TIMES.
      lo_coupon->send_customer_coin(
      EXPORTING
        it_goldcoin = lt_goldcoin
      IMPORTING
         ev_json   = DATA(lv_json)
         ot_result = DATA(lt_rerult)
         es_msg = DATA(ls_msg)
          ).
      READ TABLE lt_rerult INTO DATA(ls_rerult) INDEX 1.
      IF ls_rerult-success  EQ 'true' OR ls_rerult-success EQ 'X'.
        EXIT.
      ENDIF.
      CLEAR:ls_rerult,lt_rerult.
    ENDDO.

    IF ls_msg-type = 'E'.
      ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_coin_hq'
                                                eventdesc = '总部营销-发放金币'
                                                status = 'E'
                                                message = '调用接口发放金币失败'
                                                key1 = iv_spno
                                                key2 = lv_code ).

    ELSE.
      ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_coupon_hq'
                                                eventdesc = '总部营销-发放金币'
                                                status = 'S'
                                                message = '发放金币成功'
                                                key1 = iv_spno
                                                key2 = lv_code
                                                content = lv_json  ).  "成功的记录一下发放的集合
      "处理返回数据
      LOOP AT lt_rerult INTO ls_rerult ."这里肯定是没发放的客户
        CLEAR:ls_s .
        IF ls_rerult-success EQ 'true' OR ls_rerult-success EQ 'X'."没成功的不记录，下次读不到，合理
          READ TABLE lt_cuslist INTO ls_cus WITH KEY companycode = ls_rerult-companycode
                                                     BINARY SEARCH.
          IF sy-subrc = 0.
            MOVE-CORRESPONDING ls_cus TO ls_s .
            ls_s-coinsuccess = ls_cus-coincount . "如果成功，肯定是全部发放了
            ls_s-sendstatus  = 'Y'."发放成功
            ls_s-senddate = ls_s-zcrt_bdate = ls_s-zupd_bdate = sy-datum.
            ls_s-sendtime = ls_s-zcrt_btime = ls_s-zupd_btime = sy-uzeit.
            ls_s-zcrt_uname = ls_s-zupd_uname = '企业微信'.

            MODIFY ztint_dp_pi_s FROM ls_s."发放的记录表
          ENDIF.
        ELSE.
        ENDIF .
        CLEAR:ls_rerult,ls_s.
      ENDLOOP.

    ENDIF.
*-本流程所有客户都处理完，判断整个流程的H表状态
    DATA:lv_count TYPE i .
    SELECT SUM( coinsuccess )
        FROM ztint_dp_pi_s
        INTO @DATA(lv_send)  "本流程成功发放的
        WHERE guid = @ls_ztint_req-guid.

*本流程需要发放的
    lv_count  = REDUCE i( INIT x = 0
                        FOR ls IN lt_cuslist
                        NEXT x = x + ls-coincount ).

    IF lv_send = lv_count . "只有已发的 = 要发的 ,才修改抬头表位Y
      "审批抬头表
      ls_ztint_dp_pi_h-sendstatus = 'Y'."发放成功
      ls_ztint_dp_pi_h-sendmsg = '总部营销-金币发放成功'.

      "营销管理主信息表
      ls_ztint_req-msg        =  '总部营销-金币发放成功'.
      ls_ztint_req-status     = 'APPROVE'."已审批
      ls_ztint_req-sendstatus = 'Y'."发放成功
      ls_ztint_req-senddate   = sy-datum."发放日期
      ls_ztint_req-zupd_bdate = sy-datum.
      ls_ztint_req-zupd_btime = sy-uzeit.
      ls_ztint_req-zupd_uname = '企业微信'.

      IF ls_ztint_req-userid IS NOT INITIAL. "申请人userid

        "通知申请人额度生效
        CLEAR lv_date_s.
        CLEAR lv_content.
        lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
        lv_content =   '开思助手 \n '   && '您申请的【总部营销发放金币】已发放成功，请知悉！ \n '
        && lv_date_s.

        CLEAR ls_user_content.
        ls_user_content-content = lv_content.
        ls_user_content-userid  = ls_ztint_req-userid.
*      ls_user_content-orderno = |{ ls_ztint_req-companycode }|.
*      ls_user_content-orderdesc = |{ ls_ztint_req-companyname }|.
        ls_user_content-firstcatgory  = '客户管理'.
        ls_user_content-secondcatgory = '客户金币发放申请审批'.
        APPEND ls_user_content TO lt_user_content.
      ENDIF.

    ELSE.
      "审批抬头表
      IF lv_send NE 0.
        ls_ztint_req-sendstatus = ls_ztint_dp_pi_h-sendstatus = 'P'."部分发放
        ls_ztint_req-msg = ls_ztint_dp_pi_h-sendmsg  = '金币部分发放'.
      ELSE.
        ls_ztint_req-sendstatus = ls_ztint_dp_pi_h-sendstatus = 'N'."发放失败
        ls_ztint_req-msg = ls_ztint_dp_pi_h-sendmsg  = '金币发放失败'.
      ENDIF.

      ls_ztint_req-senddate = ls_ztint_dp_pi_h-senddate = sy-datum.
      ls_ztint_dp_pi_h-sendtime = sy-uzeit.

      "营销管理主信息表
      ls_ztint_req-status = 'APPROVE'."已审批

      ls_ztint_req-zupd_bdate = sy-datum.
      ls_ztint_req-zupd_btime = sy-uzeit.
      ls_ztint_req-zupd_uname = '企业微信'.

      "推送助手值班人员处理ztint_push_u_cf表配置
      CLEAR lv_date_s.
      CLEAR lv_content.
      lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

      lv_alert_content = '开思助手 \n '  && ls_ztint_dp_pi_h-title  && '审批后发放金币有异常,请关注! \n '
      && '实例号：' && ls_ztint_dp_pi_h-processinstanceid && ' \n '
      && '流程code：' && ls_ztint_dp_pi_h-processcode && ' \n '
      && lv_date_s.

      lv_fname = 'DPAPPLYSPECIALCOUPON'.
      lv_title =  '开思助手'.
      lv_msg_type = 'text'.

      CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
        EXPORTING
          iv_content = lv_alert_content
          iv_fname   = lv_fname.
    ENDIF.

    "给值到调用点
    is_dp_pi_h  = ls_ztint_dp_pi_h .

    "保存信息
    MODIFY ztint_market_req FROM ls_ztint_req.        "审批流程主信息表
    MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.      "钉钉审批已审批抬头
    MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i."钉钉审批已审批详情
    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.      "发放log

    "解锁表
    CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
      EXPORTING
        mode_ztint_dp_pi_h = 'E'
        mandt              = sy-mandt
        instanceid         = lv_instance.
    .
    "推送通知
    IF lt_user_content IS NOT INITIAL.
      lv_title =  '开思助手'.
      lv_msg_type = 'text'.
      CONDENSE lv_url NO-GAPS .

      CALL FUNCTION 'Z_FMQYWX_CASSINT_PUSH_MESSAGE'
*        IN UPDATE TASK
        EXPORTING
          iv_title    = lv_title
          iv_msgtype  = lv_msg_type
          iv_url      = lv_url
        TABLES
          it_userlist = lt_user_content.
    ENDIF.

  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->SEND_ICEC_CUSTOMER_COIN_V2
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* | [--->] IV_CODE                        TYPE        STRING(optional)
* | [--->] IV_NUMBER                      TYPE        INT4(optional)
* | [--->] IV_NUMWITHDECIMAL              TYPE        DMBTR(optional)
* | [--->] IS_DETAIL                      TYPE        TS_APPROVALDETAIL(optional)
* | [<---] IS_DP_PI_H                     TYPE        ZTINT_DP_PI_H
* | [<---] IV_SUCCESSNUM                  TYPE        INT4
* +--------------------------------------------------------------------------------------</SIGNATURE>
METHOD send_icec_customer_coin_v2.

  DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h, "钉钉审批已审批抬头
        ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
        lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i, "钉钉审批已审批详情
        ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
        lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l, "钉钉审批已审批log
        ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.

  DATA:lt_user_content TYPE  zsint_push_userlist_tab,
       ls_user_content TYPE  zsint_push_userlist.

  DATA:ls_list     TYPE zcl_icec_coupon_api=>ty_send_goldcoin,
       lt_goldcoin TYPE STANDARD TABLE OF zcl_icec_coupon_api=>ty_send_goldcoin.
  DATA:lv_body          TYPE string,
       lv_timestamp     TYPE p,
       lv_instance      TYPE zde_intprocessinstanceid,
       lv_alert_content TYPE string,
       lv_fname         TYPE string,
       lv_url           TYPE string,
       lv_title         TYPE string,
       lv_msg_type      TYPE string,
       lv_content       TYPE string,
       lv_date_s        TYPE string,
       lv_companyname   TYPE string,
       lv_provideway    TYPE string.
  DATA:lt_msglist TYPE STANDARD TABLE OF ztint_msg_list,
       ls_msglist TYPE ztint_msg_list.
  DATA:lv_status TYPE string,
       lv_code   TYPE string.

* B阶梯返利 、D售后安抚、 F特殊场景  发金币
* 阶梯返利 申请发金币时只是备案，审批同意后不做发放动作，两个月后考核合格发放金币
  IF iv_numwithdecimal IS NOT INITIAL."如果入参传了带小数位的则发放时带小数位
    iv_number = iv_numwithdecimal.
  ENDIF.
  IF is_detail IS INITIAL . "开始不发放，审核通过后发放时，单独调用一次【二期发放使用】
    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).  "审批流的实例单号202503190002
    lv_code   = ls_detail-info-template_id. "模版code
    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail_new'
                           zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败'
                           zvalue1 = iv_spno
                           zvalue2 = lv_code
                           sendmsg = 'X'
                           fname = 'BULELIST' ).
      RETURN.
    ENDIF.
  ELSE.
    ls_detail = is_detail .
    lv_code = iv_code .
  ENDIF.

  lv_instance = iv_spno.

  lv_status = COND #( WHEN iv_type EQ 'agree' THEN 'APPROVE'  "同意
                      WHEN iv_type EQ 'refuse' THEN  'UNAPPROVE' "驳回
                      WHEN iv_type EQ 'terminate' THEN 'CANCELED' ELSE '' ). "撤销

*审批结果为 agree 发放
*        SELECT SINGLE * FROM ztint_dp_code
*          INTO @DATA(ls_ztint_dp_code)
*          WHERE processcode = @lv_code.

  "取配置的流程字段
  SELECT * FROM ztint_dp_field
    INTO TABLE @DATA(lt_ztint_dp_field)
    WHERE processcode = @lv_code.
  SORT lt_ztint_dp_field BY dingname.

  "锁表
  CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
    EXPORTING
      mode_ztint_dp_pi_h = 'E'
      mandt              = sy-mandt
      instanceid         = lv_instance.

  IF sy-subrc <> 0.
    "锁表失败 记录日志
    ls_ztint_dp_pi_l   = prepare_ztint_dp_pi_l( eventid   = 'lock_ztint_dp_pi_h'
                                     eventdesc = '营销管理-发放金币锁表失败'
                                     status  = 'E'
                                     message = '实例发放金币锁表失败'
                                     key1 = iv_spno
                                     key2 = lv_code ).

    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
    RETURN.
  ENDIF.

  "没发过
  "抬头信息
  ls_ztint_dp_pi_h-processinstanceid = ls_detail-info-sp_no. "审批流程ID
  ls_ztint_dp_pi_h-processcode       = ls_detail-info-template_id.
  ls_ztint_dp_pi_h-title             = ls_detail-info-sp_name.
*    ls_ztint_dp_pi_h-businessid = ls_detail-business_id.
  ls_ztint_dp_pi_h-zresult = iv_type. "agree  审批结果

  lv_timestamp  = ls_detail-info-apply_time."创建时间
  IF lv_timestamp > 0.
    zcl_cassint_formatter=>convert_unix_timestamp_to_abap(
      EXPORTING unixsecs = lv_timestamp
      IMPORTING ev_date = ls_ztint_dp_pi_h-createdate ev_time = ls_ztint_dp_pi_h-createtime ).
    ls_ztint_dp_pi_h-createstamp = lv_timestamp.
    CONDENSE ls_ztint_dp_pi_h-createstamp.
  ENDIF.
  CLEAR lv_timestamp.

  DATA(lv_notes) = lines( ls_detail-info-sp_record )."多少审批节点

  DATA(lt_time) = ls_detail-info-sp_record[ lv_notes ]-details.
  SORT lt_time BY sptime DESCENDING.

  READ TABLE lt_time INTO DATA(ls_time) INDEX 1.
  lv_timestamp = ls_time-sptime.
  IF lv_timestamp > 0.
    zcl_cassint_formatter=>convert_unix_timestamp_to_abap(
      EXPORTING unixsecs = lv_timestamp
      IMPORTING ev_date = ls_ztint_dp_pi_h-finishdate ev_time = ls_ztint_dp_pi_h-finishtime ).
    ls_ztint_dp_pi_h-finishstamp = lv_timestamp.
    CONDENSE ls_ztint_dp_pi_h-finishstamp.
  ENDIF.
  CLEAR lv_timestamp.

  ls_ztint_dp_pi_h-status = 'finish'."流程状态
  ls_ztint_dp_pi_h-originatordeptid = ls_detail-info-applyer-partyid."发起部门
  ls_ztint_dp_pi_h-originatoruserid = ls_detail-info-applyer-userid. "申请人ID

  "审批详情
  LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).

    CLEAR ls_ztint_dp_pi_i.
    ls_ztint_dp_pi_i-processinstanceid = iv_spno.  "实例名称
    ls_ztint_dp_pi_i-posnr = sy-tabix.
    ls_ztint_dp_pi_i-name = VALUE #( ls_content-title[ lang = 'zh_CN' ]-text OPTIONAL ). "中文名

    CASE ls_content-control.
      WHEN 'Text' OR 'Textarea'.
        ls_ztint_dp_pi_i-value = ls_content-value-text.
      WHEN 'Number'.
        ls_ztint_dp_pi_i-value = ls_content-value-new_number.
      WHEN 'Money'.
        ls_ztint_dp_pi_i-value = ls_content-value-new_money.
      WHEN 'Selector'.
        ls_ztint_dp_pi_i-value = VALUE #( ls_content-value-selector-options[ 1 ]-value[ 1 ]-text OPTIONAL ).
      WHEN  OTHERS.
    ENDCASE.

    APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.  "审批的每个字段的值

    "准备调用接口数据
    READ TABLE lt_ztint_dp_field INTO DATA(ls_ztint_dp_field) WITH  KEY dingname = ls_ztint_dp_pi_i-name BINARY SEARCH.
    IF sy-subrc = 0.
      CASE  ls_ztint_dp_field-name.
        WHEN 'companycode'. "公司code
          "转大写  去空格
          ls_list-companycode = ls_ztint_dp_pi_i-value.
          CONDENSE ls_list-companycode NO-GAPS .
          TRANSLATE ls_list-companycode TO UPPER CASE.
          SELECT SINGLE companyid INTO ls_list-companyid
            FROM ztint_cus_inf WHERE companycode = ls_list-companycode.

        WHEN 'number' ."发放数量【阶梯返利也会发放金币】
          IF iv_number NE 0.
            ls_list-number = iv_number.  "二期使用，阶梯返利达标后外层传入发放的金币数量
          ELSE.
            REPLACE ALL OCCURRENCES OF  '元' IN ls_ztint_dp_pi_i-value WITH space .
            ls_list-number = ls_ztint_dp_pi_i-value.
          ENDIF.

        WHEN 'orderid'."订单号

          ls_list-orderid = ls_ztint_dp_pi_i-value. "售后赔付的订单号

        WHEN 'mltype'. "发放理由——维修厂可见 ，给值营销类别
          ls_list-providereason = ls_ztint_dp_pi_i-value .

        WHEN 'remark'."备注 内部人员查看  —— 申请理由

          ls_list-remark = zcl_cassint_formatter=>filter_emoji_string( EXACT string( ls_ztint_dp_pi_i-value ) ).

        WHEN 'companyname'."维修厂名称
          lv_companyname = ls_ztint_dp_pi_i-value.
        WHEN OTHERS.
      ENDCASE.
    ENDIF.
    "发放理由——维修厂可见 ，申请类型

    CLEAR ls_ztint_dp_field.
  ENDLOOP.

  IF ls_list-number IS INITIAL AND iv_number NE 0.
    ls_list-number = iv_number.  "二期使用，阶梯返利达标后外层传入发放的金币数量
  ENDIF.

  "获取发起人名称
  SELECT SINGLE username,userid
     FROM ztint_user_inf
     INTO ( @DATA(lv_username),@DATA(lv_userid) )
    WHERE qywxuserid = @ls_detail-info-applyer-userid.
  IF lv_username IS INITIAL.
    lv_username = '企业微信'.
  ELSE.
    ls_list-createdby = lv_username.
  ENDIF.

  "发金币要用固定的ID
  SELECT SINGLE dingprocesscode
    FROM ztint_dp_code
    INTO @ls_list-approveflowid WHERE processcode = @lv_code.

  APPEND ls_list TO lt_goldcoin.

  DATA(lo_coupon) = NEW zcl_icec_coupon_api( ).
  DO 3 TIMES.
    lo_coupon->send_customer_coin( EXPORTING it_goldcoin = lt_goldcoin
                                   IMPORTING ot_result = DATA(lt_rerult)
                                             es_msg = DATA(ls_msg) ).
    READ TABLE lt_rerult INTO DATA(ls_rerult) INDEX 1.
    IF ls_rerult-success  EQ 'true' OR ls_rerult-success EQ 'X'.
      EXIT.
    ENDIF.
    CLEAR:ls_rerult,lt_rerult.
  ENDDO.

  IF ls_rerult-success EQ 'true' OR ls_rerult-success EQ 'X'.
    "发放金币成功
    ls_ztint_dp_pi_h-sendstatus = 'Y'. "
    ls_ztint_dp_pi_h-senddate = sy-datum.
    ls_ztint_dp_pi_h-sendtime = sy-uzeit.
    ls_ztint_dp_pi_h-sendmsg = '发放成功'.

    "记录日志
    ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'send_coin_success_new'
                                      eventdesc = '营销管理-发放金币成功'
                                      status = 'S'
                                      message = '发放金币成功'
                                      key1 = iv_spno
                                      key2 = lv_code
                                      key3 =  EXACT string( ls_list-number ) )."本次发放数量
    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
    iv_successnum = ls_list-number."接口出参返回具体发放成功数量
    "更新成功发放数量
    SELECT SINGLE * INTO @DATA(ls_market_req)
      FROM ztint_market_req
      WHERE instanceid = @iv_spno.
    IF sy-subrc EQ 0.
      ls_market_req-coinsuccess = ls_market_req-coinsuccess + ls_list-number.
      IF ls_market_req-coinsuccess >= ls_market_req-coinamount.
        ls_market_req-sendstatus = 'Y'."已发放
      ELSE.
        ls_market_req-sendstatus = 'P'. "部分发放
      ENDIF.
      MODIFY ztint_market_req  FROM ls_market_req.
    ENDIF.

    "推送提醒
    IF lv_userid IS NOT INITIAL.
      "通知申请人金币额度生效
      CLEAR lv_date_s.
      CLEAR lv_content.
      lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
      lv_content =   '开思助手 \n '   && ls_ztint_dp_pi_h-title && '金币已发放，请知悉！ \n '
       && '审批编码:' && ls_ztint_dp_pi_h-processinstanceid && ' \n '
       && '客户名称:' && lv_companyname && ' \n '
       && '客户代码:' && ls_list-companycode && ' \n '
       && '金币:' && ls_list-number  &&  ' \n '
       && lv_date_s.

      CLEAR ls_user_content.
      ls_user_content-content = lv_content.
      ls_user_content-userid = lv_userid.
      ls_user_content-orderno = |{ ls_list-companycode }|.
      ls_user_content-orderdesc = |{ lv_companyname }|.
      ls_user_content-firstcatgory = '客户管理'.
      ls_user_content-secondcatgory = '客户金币发放申请审批'.
      APPEND ls_user_content TO lt_user_content.

    ENDIF.
  ELSE.

    "发放金币失败
    ls_ztint_dp_pi_h-sendstatus = 'N'. "没发放
    ls_ztint_dp_pi_h-senddate = sy-datum.
    ls_ztint_dp_pi_h-sendtime = sy-uzeit.
    IF ls_rerult-remark IS INITIAL.
      ls_rerult-remark = '发放失败'.
    ENDIF.
    ls_ztint_dp_pi_h-sendmsg = ls_rerult-remark.

    "记录日志
    ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'send_coin_fail_new'
                                                  eventdesc = '营销管理-发放金币失败'
                                                  status = 'E'
                                                  message = '发放金币失败'
                                                  key1 = iv_spno
                                                  key2 = lv_code ).
    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.

    "推送助手值班人员处理
    CLEAR lv_date_s.
    CLEAR lv_content.
    lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

    lv_alert_content = '开思助手 \n '  && ls_ztint_dp_pi_h-title  && '审批后调用电商接口失败,请关注! \n '
    && '实例号：' && ls_ztint_dp_pi_h-processinstanceid && ' \n '
    && '流程code：' && ls_ztint_dp_pi_h-processcode && ' \n '
    && lv_date_s.

    lv_fname = 'DPAPPLYSPECIALCOUPON'."取通知优惠券的人员即可
    lv_title =  '开思助手'.
    lv_msg_type = 'text'.

    CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
      EXPORTING
        iv_content = lv_alert_content
        iv_fname   = lv_fname.

  ENDIF.

  "给值到调用点
  is_dp_pi_h  = ls_ztint_dp_pi_h .

  "保存信息
  MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.      "钉钉审批已审批抬头
  MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i."钉钉审批已审批详情

  "解锁表
  CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
    EXPORTING
      mode_ztint_dp_pi_h = 'E'
      mandt              = sy-mandt
      instanceid         = lv_instance.
  .
  "推送通知
  IF lt_user_content IS NOT INITIAL.
    lv_title =  '开思助手'.
    lv_msg_type = 'text'.
    CONDENSE lv_url NO-GAPS .

    CALL FUNCTION 'Z_FMQYWX_CASSINT_PUSH_MESSAGE'
*        IN UPDATE TASK
      EXPORTING
        iv_title    = lv_title
        iv_msgtype  = lv_msg_type
        iv_url      = lv_url
      TABLES
        it_userlist = lt_user_content.
  ENDIF.

ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->SEND_ICEC_CUSTOMER_COIN_V2_NEW
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* | [--->] IV_CODE                        TYPE        STRING(optional)
* | [--->] IV_NUMBER                      TYPE        INT4(optional)
* | [--->] IV_NUMWITHDECIMAL              TYPE        DMBTR(optional)
* | [--->] IV_USERLOGINID                 TYPE        STRING(optional)
* | [--->] IS_DETAIL                      TYPE        TS_APPROVALDETAIL(optional)
* | [<---] IS_DP_PI_H                     TYPE        ZTINT_DP_PI_H
* | [<---] IV_SUCCESSNUM                  TYPE        INT4
* +--------------------------------------------------------------------------------------</SIGNATURE>
METHOD send_icec_customer_coin_v2_new.
*进入此函数就是发放，至于发不发放已经在外层判断了
*金币发放的逻辑，都存放ZTINT_DP_PI_S表
*接口地址 http://ctsp.casstime.com/interface/manage/detail?id=14428&model_id=24582&classify=2483&method=POST&product_id=1
*【注意：本接口支持发放小数】
  DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h, "审批发放抬头
        ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,

        lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i, "审批流的字段值详情
        ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,

        lt_ztint_dp_pi_s TYPE STANDARD TABLE OF ztint_dp_pi_s, "审批发放明细详情（优惠券、金币、积分）
        ls_ztint_dp_pi_s LIKE LINE OF lt_ztint_dp_pi_s,

        lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l, "钉钉审批已审批log
        ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.

  DATA:lt_user_content TYPE  zsint_push_userlist_tab,
       ls_user_content TYPE  zsint_push_userlist.

  DATA:ls_list     TYPE zcl_icec_coupon_api=>ty_send_goldcoin,
       lt_goldcoin TYPE STANDARD TABLE OF zcl_icec_coupon_api=>ty_send_goldcoin.
  DATA:lv_body          TYPE string,
       lv_timestamp     TYPE p,
       lv_instance      TYPE zde_intprocessinstanceid,
       lv_alert_content TYPE string,
       lv_fname         TYPE string,
       lv_url           TYPE string,
       lv_title         TYPE string,
       lv_msg_type      TYPE string,
       lv_content       TYPE string,
       lv_date_s        TYPE string,
       lv_companyname   TYPE string,
       lv_orderid   TYPE string,
       lv_provideway    TYPE string.
  DATA:lt_msglist TYPE STANDARD TABLE OF ztint_msg_list,
       ls_msglist TYPE ztint_msg_list.
  DATA:lv_status TYPE string,
       lv_code   TYPE string.

* B阶梯返利 、D售后安抚、 F特殊场景  发金币
* 阶梯返利 申请发金币时只是备案，审批同意后不做发放动作，两个月后考核合格发放金币

  IF iv_numwithdecimal IS NOT INITIAL."如果入参传了带小数位的则发放时带小数位
    iv_number = iv_numwithdecimal.
  ENDIF.

  IF is_detail IS INITIAL . "开始不发放，审核通过后发放时，单独调用一次【二期发放使用】
    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).  "审批流的实例单号202503190002
    lv_code   = ls_detail-info-template_id. "模版code
    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail_new'
                           zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败'
                           zvalue1 = iv_spno
                           zvalue2 = lv_code
                           sendmsg = 'X'
                           fname = 'BULELIST' ).
      RETURN.
    ENDIF.
  ELSE.
    ls_detail = is_detail . "申请直接发放 &&  备案申请后的发放审批流发放
    lv_code = iv_code .
  ENDIF.

*-判断主表是否有申请记录
  "获取申请记录（及发放）
  SELECT SINGLE * FROM ztint_market_req
    INTO @DATA(ls_market_req)
    WHERE instanceid = @iv_spno.
  IF sy-subrc NE 0.
    "没找到 记录日志
    ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_coin'
                                              eventdesc = '总部营销-发放金币'
                                              status = 'E'
                                              message = '未找到对应的申请记录'
                                              key1 = iv_spno
                                              key2 = lv_code ).
    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
    RETURN.
  ENDIF.


*判断是否已经全部发放过
  SELECT SINGLE *
    FROM ztint_dp_pi_h
    INTO CORRESPONDING FIELDS OF @ls_ztint_dp_pi_h
    WHERE processinstanceid = @iv_spno.

  IF ls_ztint_dp_pi_h-sendstatus = 'Y' "已发放过（备案审批流直接发放的）
     OR ls_market_req-sendstatus = 'Y' . "因为先备案，后发放审批流发放的，原来的备案审批流是没有发放记录的！！！
    ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_coin_hq'
                                              eventdesc = '总部营销-发放金币'
                                              status = 'E'
                                              message = '实例已申请发放过金币'
                                              key1 = iv_spno
                                              key2 = lv_code ).

    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
    RETURN.
  ENDIF.


  lv_instance = iv_spno.

  "取配置的流程字段
  SELECT * FROM ztint_dp_field
    INTO TABLE @DATA(lt_ztint_dp_field)
    WHERE processcode = @lv_code.
  SORT lt_ztint_dp_field BY dingname.

  "锁表
  CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
    EXPORTING
      mode_ztint_dp_pi_h = 'E'
      mandt              = sy-mandt
      instanceid         = lv_instance.

  IF sy-subrc <> 0.
    "锁表失败 记录日志
    ls_ztint_dp_pi_l   = prepare_ztint_dp_pi_l( eventid   = 'lock_ztint_dp_pi_h'
                                     eventdesc = '营销管理-发放金币锁表失败'
                                     status  = 'E'
                                     message = '实例发放金币锁表失败'
                                     key1 = iv_spno
                                     key2 = lv_code ).

    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
    RETURN.
  ENDIF.

  "没发过
  "抬头信息
  ls_ztint_dp_pi_h-processinstanceid = ls_detail-info-sp_no. "审批流程ID
  ls_ztint_dp_pi_h-processcode       = ls_detail-info-template_id.
  ls_ztint_dp_pi_h-title             = ls_detail-info-sp_name.
*    ls_ztint_dp_pi_h-businessid = ls_detail-business_id.
  ls_ztint_dp_pi_h-zresult = iv_type. "agree  审批结果

  lv_timestamp  = ls_detail-info-apply_time."创建时间
  IF lv_timestamp > 0.
    zcl_cassint_formatter=>convert_unix_timestamp_to_abap(
      EXPORTING unixsecs = lv_timestamp
      IMPORTING ev_date = ls_ztint_dp_pi_h-createdate ev_time = ls_ztint_dp_pi_h-createtime ).
    ls_ztint_dp_pi_h-createstamp = lv_timestamp.
    CONDENSE ls_ztint_dp_pi_h-createstamp.
  ENDIF.
  CLEAR lv_timestamp.

  DATA(lv_notes) = lines( ls_detail-info-sp_record )."多少审批节点

  DATA(lt_time) = ls_detail-info-sp_record[ lv_notes ]-details.
  SORT lt_time BY sptime DESCENDING.

  READ TABLE lt_time INTO DATA(ls_time) INDEX 1.
  lv_timestamp = ls_time-sptime.
  IF lv_timestamp > 0.
    zcl_cassint_formatter=>convert_unix_timestamp_to_abap(
      EXPORTING unixsecs = lv_timestamp
      IMPORTING ev_date = ls_ztint_dp_pi_h-finishdate ev_time = ls_ztint_dp_pi_h-finishtime ).
    ls_ztint_dp_pi_h-finishstamp = lv_timestamp.
    CONDENSE ls_ztint_dp_pi_h-finishstamp.
  ENDIF.
  CLEAR lv_timestamp.

  ls_ztint_dp_pi_h-status = 'finish'."流程状态
  ls_ztint_dp_pi_h-originatordeptid = ls_detail-info-applyer-partyid."发起部门
  ls_ztint_dp_pi_h-originatoruserid = ls_detail-info-applyer-userid. "申请人ID

  "审批详情
  LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).

    CLEAR ls_ztint_dp_pi_i.
    ls_ztint_dp_pi_i-processinstanceid = iv_spno.  "实例名称
    ls_ztint_dp_pi_i-posnr = sy-tabix.
    ls_ztint_dp_pi_i-name = VALUE #( ls_content-title[ lang = 'zh_CN' ]-text OPTIONAL ). "中文名

    CASE ls_content-control.
      WHEN 'Text' OR 'Textarea'.
        ls_ztint_dp_pi_i-value = ls_content-value-text.
      WHEN 'Number'.
        ls_ztint_dp_pi_i-value = ls_content-value-new_number.
      WHEN 'Money'.
        ls_ztint_dp_pi_i-value = ls_content-value-new_money.
      WHEN 'Selector'.
        ls_ztint_dp_pi_i-value = VALUE #( ls_content-value-selector-options[ 1 ]-value[ 1 ]-text OPTIONAL ).
      WHEN  OTHERS.
    ENDCASE.

    APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.  "审批的每个字段的值

*    "准备调用接口数据
    READ TABLE lt_ztint_dp_field INTO DATA(ls_ztint_dp_field) WITH  KEY dingname = ls_ztint_dp_pi_i-name BINARY SEARCH.
    IF sy-subrc = 0.
      CASE  ls_ztint_dp_field-name.
        WHEN 'number' ."发放数量【阶梯返利也会发放金币】
          IF iv_number NE 0.
            ls_list-number = iv_number.  "二期使用，阶梯返利达标后外层传入发放的金币数量
          ELSE.
            REPLACE ALL OCCURRENCES OF  '元' IN ls_ztint_dp_pi_i-value WITH space .
            ls_list-number = ls_ztint_dp_pi_i-value.
          ENDIF.
        WHEN 'companyname'."维修厂名称
          lv_companyname = ls_ztint_dp_pi_i-value.
        WHEN 'orderid'."订单编号
          lv_orderid = ls_ztint_dp_pi_i-value.
        WHEN OTHERS.
      ENDCASE.
    ENDIF.
    CLEAR ls_ztint_dp_field.
  ENDLOOP.


  "获取发起人名称
  SELECT SINGLE username,userid
     FROM ztint_user_inf
     INTO ( @DATA(lv_username),@DATA(lv_userid) )
    WHERE qywxuserid = @ls_detail-info-applyer-userid.
  IF lv_username IS INITIAL.
    lv_username = '企业微信'.
  ENDIF.

  "发金币要用固定的ID
  SELECT SINGLE dingprocesscode
    FROM ztint_dp_code
    INTO @DATA(lv_id)
    WHERE processcode = @lv_code.

*-已发放的清单
*  disinstanceid  发放审批流 和 自身guid对应
*  instanceid     原报备审批流

  SELECT *   "唯一一条
    FROM ztint_dp_pi_s
    INTO TABLE @DATA(lt_s)
    WHERE guid = @ls_market_req-guid."含申请流发放 && 备案后的发放审批流
  SORT lt_s BY disinstanceid .

*发放数据整理

  ls_list-companycode = ls_market_req-companycode .
  "公司code —>companyid
  SELECT SINGLE companyid
    INTO ls_list-companyid
    FROM ztint_cus_inf WHERE companycode = ls_market_req-companycode.

  "发放数量(1、除阶梯返利外的，一次全部发 2、阶梯返利可能部分发)
  "金币发放数量coinamount全部要发的数量

  "阶梯返利达标后外层传入发放的金币数量，说明是阶梯返利报表传入的金额
  IF ls_list-number IS INITIAL AND iv_number NE 0.
    ls_list-number = iv_number.
  ENDIF.

  "发放理由——维修厂可见 ，给值营销类别
  SELECT SINGLE mlname INTO @ls_list-providereason
    FROM ztint_mltype
    WHERE mltype = @ls_market_req-mltype.

  "订单编号
    ls_list-orderid = lv_orderid.

  "申请理由 —— 给值备注 内部人员查看
  ls_list-remark = ls_market_req-reqreason .

  "发金币要用固定的ID
  ls_list-approveflowid = lv_id .

  "创建人
  ls_list-createdby = lv_username.

  APPEND ls_list TO lt_goldcoin.

  DATA(lo_coupon) = NEW zcl_icec_coupon_api( ).

  DO 3 TIMES.
    lo_coupon->send_customer_coin( EXPORTING it_goldcoin = lt_goldcoin
                                   IMPORTING ot_result = DATA(lt_rerult)
                                             es_msg = DATA(ls_msg) ).
    READ TABLE lt_rerult INTO DATA(ls_rerult) INDEX 1.
    IF ls_rerult-success  EQ 'true' OR ls_rerult-success EQ 'X'.
      EXIT.
    ENDIF.
    CLEAR:ls_rerult,lt_rerult.
  ENDDO.

  IF ls_msg-type = 'E'.
    ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_coin_hq'
                                              eventdesc = '总部营销-发放金币'
                                              status = 'E'
                                              message = '调用接口发放金币失败'
                                              key1 = iv_spno
                                              key2 = lv_code ).
    "推送助手值班人员处理
    CLEAR lv_date_s.
    CLEAR lv_content.
    lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

    lv_alert_content = '开思助手 \n '  && ls_ztint_dp_pi_h-title  && '审批后调用电商接口失败,请关注! \n '
    && '实例号：' && ls_ztint_dp_pi_h-processinstanceid && ' \n '
    && '流程code：' && ls_ztint_dp_pi_h-processcode && ' \n '
    && lv_date_s.

    lv_fname = 'DPAPPLYSPECIALCOUPON'."取通知优惠券的人员即可
    lv_title =  '开思助手'.
    lv_msg_type = 'text'.

    CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
      EXPORTING
        iv_content = lv_alert_content
        iv_fname   = lv_fname.

  ELSE.
    "记录日志
    ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'send_coin_success_new'
                                      eventdesc = '营销管理-发放金币成功'
                                      status = 'S'
                                      message = '发放金币成功'
                                      key1 = iv_spno
                                      key2 = lv_code
                                      key3 =  EXACT string( ls_list-number ) )."本次发放数量

    IF ls_rerult-success EQ 'true' OR ls_rerult-success EQ 'X'.

      IF ls_market_req-marketingtype = '3' . "对于发放审批流的发放，要反写报备流
        SELECT SINGLE *
          FROM ztint_market_req
          INTO @DATA(ls_source)
          WHERE instanceid = @ls_market_req-sourceinstanceid.
        IF sy-subrc = 0.
          ls_source-coinsuccess = ls_source-coinsuccess + ls_list-number.
          IF ls_source-coinamount = ls_source-coinsuccess .
            ls_source-sendstatus = 'Y'.
            ls_source-sendmsg = '金币已发放'.
            ls_source-senddate = sy-datum.
          ELSE.
            ls_source-sendstatus = 'P'.
            ls_source-sendmsg = '金币部分发放'.
            ls_source-senddate = sy-datum.
          ENDIF.
          UPDATE ztint_market_req SET   coinsuccess = ls_source-coinsuccess
                                        sendstatus = ls_source-sendstatus
                                        sendmsg    = ls_source-sendmsg
                                        senddate = ls_source-senddate
                                  WHERE guid = ls_source-guid .
          COMMIT WORK .
        ENDIF.
      ENDIF.

      READ TABLE lt_s INTO DATA(ls_s) WITH KEY disinstanceid = ls_market_req-instanceid
                                           BINARY SEARCH.
      IF sy-subrc = 0."读到更新【这个逻辑处理返利的多次发放】
        ls_s-couponsucess   = ls_s-couponsucess + ls_list-number. "已发的 + 本次成功的
        IF ls_s-coincount = ls_s-couponsucess ."
          ls_s-sendstatus  = 'Y'."发放成功
        ELSE.
          ls_s-sendstatus  = 'P'."部分发放
        ENDIF.
        ls_s-zupd_bdate = sy-datum.
        ls_s-zupd_btime = sy-uzeit.
        ls_s-zupd_uname = '企业微信'.

      ELSE."没读到新增
        CLEAR:ls_s.
        ls_s-guid = ls_market_req-guid .
        ls_s-companycode = ls_market_req-companycode .
        ls_s-disinstanceid = ls_market_req-instanceid .   "发放审理流(1连队的发放是其本身)
        ls_s-instanceid = ls_market_req-sourceinstanceid ."原报备审批流
        ls_s-vtext = ls_market_req-companyname .
        ls_s-coincount =   ls_market_req-coinamount."要发放
        ls_s-coinsuccess = ls_list-number.
        IF ls_s-coincount = ls_list-number.
          ls_s-sendstatus  = 'Y'."发放成功
        ELSE.
          ls_s-sendstatus  = 'P'."部分发放
        ENDIF.

        ls_s-senddate = ls_s-zcrt_bdate = ls_s-zupd_bdate = sy-datum.
        ls_s-sendtime = ls_s-zcrt_btime = ls_s-zupd_btime = sy-uzeit.
        ls_s-zcrt_uname = ls_s-zupd_uname = '企业微信'.
      ENDIF.

      "更新成功发放数量
      ls_market_req-coinsuccess = ls_market_req-coinsuccess + ls_list-number. "发放的
      iv_successnum = ls_list-number."接口出参返回具体发放成功数量

      MODIFY ztint_dp_pi_s FROM ls_s."发放的记录表
      COMMIT WORK .
    ELSE.
    ENDIF.
  ENDIF.

*判断整个流程的H表的发放状态
  DATA:lv_count TYPE i .
  SELECT SUM( coinsuccess )
      FROM ztint_dp_pi_s
      INTO @DATA(lv_send)  "本流程成功发放的
      WHERE guid = @ls_market_req-guid.

  IF lv_send = ls_market_req-coinamount .  "成功发放的 = 需要发放的
    ls_market_req-sendstatus = ls_ztint_dp_pi_h-sendstatus = 'Y'."已发放
    ls_market_req-sendmsg = ls_ztint_dp_pi_h-sendmsg  = '金币发放成功'.

    "推送提醒
    IF lv_userid IS NOT INITIAL.
      "通知申请人金币额度生效
      CLEAR lv_date_s.
      CLEAR lv_content.
      lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
      lv_content =   '开思助手 \n '   && ls_ztint_dp_pi_h-title && '金币已发放，请知悉！ \n '
       && '审批编码:' && ls_ztint_dp_pi_h-processinstanceid && ' \n '
       && '客户名称:' && lv_companyname && ' \n '
       && '客户代码:' && ls_list-companycode && ' \n '
       && '金币:' && ls_list-number  &&  ' \n '
       && lv_date_s.

      CLEAR ls_user_content.
      ls_user_content-content = lv_content.
      ls_user_content-userid = lv_userid.
      ls_user_content-orderno = |{ ls_list-companycode }|.
      ls_user_content-orderdesc = |{ lv_companyname }|.
      ls_user_content-firstcatgory = '客户管理'.
      ls_user_content-secondcatgory = '客户金币发放申请审批'.
      APPEND ls_user_content TO lt_user_content.

    ENDIF.
  ELSE.
    "审批抬头表
    IF lv_send NE 0.
      ls_market_req-sendstatus = ls_ztint_dp_pi_h-sendstatus = 'P'."部分发放
      ls_market_req-sendmsg = ls_ztint_dp_pi_h-sendmsg  = '金币部分发放'.
    ELSE.
      ls_market_req-sendstatus = ls_ztint_dp_pi_h-sendstatus = 'N'."发放失败
      ls_market_req-sendmsg = ls_ztint_dp_pi_h-sendmsg = '金币发放失败'.
    ENDIF.
  ENDIF.

  ls_market_req-senddate = ls_ztint_dp_pi_h-senddate = sy-datum."发放日期
  ls_ztint_dp_pi_h-sendtime = sy-uzeit.
  ls_market_req-status = 'APPROVE'."已审批

  ls_ztint_dp_pi_h-zcrt_bdate = ls_ztint_dp_pi_h-zupd_bdate = ls_market_req-zupd_bdate = sy-datum.
  ls_ztint_dp_pi_h-zcrt_btime = ls_ztint_dp_pi_h-zupd_btime = ls_market_req-zupd_btime = sy-uzeit.
  ls_ztint_dp_pi_h-zcrt_uname = ls_ztint_dp_pi_h-zupd_uname = ls_market_req-zupd_uname = '企业微信'.

  "给值到调用点
  is_dp_pi_h  = ls_ztint_dp_pi_h .

  "保存信息
  MODIFY ztint_market_req FROM ls_market_req.      "审批流程主信息表
  MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.      "钉钉审批已审批抬头
  MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i."钉钉审批已审批详情
  MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.  "记录发放日志

  "解锁表
  CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
    EXPORTING
      mode_ztint_dp_pi_h = 'E'
      mandt              = sy-mandt
      instanceid         = lv_instance.
  .
  "推送通知
  IF lt_user_content IS NOT INITIAL.
    lv_title =  '开思助手'.
    lv_msg_type = 'text'.
    CONDENSE lv_url NO-GAPS .

    CALL FUNCTION 'Z_FMQYWX_CASSINT_PUSH_MESSAGE'
*        IN UPDATE TASK
      EXPORTING
        iv_title    = lv_title
        iv_msgtype  = lv_msg_type
        iv_url      = lv_url
      TABLES
        it_userlist = lt_user_content.
  ENDIF.
ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->SPECHECKOUT_ICEC_CLOSE
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD specheckout_icec_close.

    DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h,
          ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
          lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i,
          ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
          lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l,
          ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
    DATA:lt_user_content TYPE  zsint_push_userlist_tab,
         ls_user_content TYPE  zsint_push_userlist.
    DATA:lv_body          TYPE string,
         lv_instance      TYPE zde_intprocessinstanceid,
         lv_alert_content TYPE string,
         lv_fname         TYPE string,
         lv_url           TYPE string,
         lv_title         TYPE string,
         lv_msg_type      TYPE string,
         lv_content       TYPE string,
         lv_date_s        TYPE string.

    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
    DATA(lv_code) = ls_detail-info-template_id.

    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
                           sendmsg = 'X' fname = 'BULELIST' ).
      RETURN.
    ENDIF.


    "审批结果
    CASE iv_type.
      WHEN 'agree'. "审批通过
        "查找申请记录
        SELECT SINGLE * FROM ztint_spechecout INTO @DATA(ls_ztint_spechecout) WHERE instanceid = @iv_spno.
        IF sy-subrc NE 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_specheckout'
                    eventdesc = '申请特殊账期' status = 'E' message = '未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.
        "判断是否已经申请过
        SELECT SINGLE * FROM ztint_dp_pi_h INTO CORRESPONDING FIELDS OF @ls_ztint_dp_pi_h
          WHERE processinstanceid = @iv_spno.
        IF ls_ztint_dp_pi_h-sendstatus = 'Y'. "已申请过
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_specheckout'
                    eventdesc = '申请特殊账期' status = 'E' message = '实例已申请过特殊账期'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.

        lv_instance = iv_spno.
        "锁表
        CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.
        IF sy-subrc <> 0.
          "锁表失败 记录日志
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l(  eventid = 'apply_specheckout'
                    eventdesc = '申请特殊账期' status = 'E' message = '实例申请特殊账期锁表失败'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.
        "抬头信息
        ls_ztint_dp_pi_h = prepare_ztint_dp_pi_h( is_detail = ls_detail iv_type = iv_type ).
        "审批详情
        LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
          CLEAR ls_ztint_dp_pi_i.
          ls_ztint_dp_pi_i = prepare_ztint_dp_pi_i( iv_spno = iv_spno is_content = ls_content ).
          APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.
        ENDLOOP.

        "获取发起人
        SELECT SINGLE username INTO @DATA(lv_username) FROM ztint_user_inf
          WHERE qywxuserid = @ls_detail-info-applyer-userid.
        IF lv_username IS INITIAL.
          lv_username = '企业微信'.
        ENDIF.

        "获取额度信息
        DATA(lo_partycredit) = NEW zcl_icec_partycredit_api( ).
        lo_partycredit->get_partycredit_detail(
          EXPORTING iv_companyid = EXACT string( ls_ztint_spechecout-companyid )
          IMPORTING ev_msg = DATA(ls_msg) es_creditdetail = DATA(ls_creditdetail) ).
        IF ls_msg-type EQ 'E'.
          ls_ztint_spechecout-msg = '获取额度信息失败'.
        ENDIF.

        "特殊账期申请调用接口
        lv_body = |\{"serviceType":"{ COND #( WHEN ls_ztint_spechecout-servicetype EQ '白条' THEN 'CASSLOAN'
                                              WHEN ls_ztint_spechecout-servicetype EQ '挂账' THEN 'COMPANY_ACCOUNT' ) }",| &&
                  |"maxDeadLine":{ ls_ztint_spechecout-applyperiod },| &&
                  |"createBy":"{ lv_username }",| &&
                  |"partyIdTo":"{ ls_ztint_spechecout-companyid }",| &&
                  |"partyIdFrom":"10424",| &&
                  |"userLoginId":"system",| &&
                  |"creditSt":"{ ls_creditdetail-creditst }",| &&
                  |"creditLimit":{ ls_creditdetail-creditlimit / 1000000 },| &&
                  |"punishAccountRate":"{ ls_creditdetail-punishaccountrate }",| &&
                  |"maxCreditLimit":{ ls_creditdetail-maxcreditlimit / 1000000 },"remark":""| &&
                  |\}|.
        lo_partycredit->update_partycredit( EXPORTING iv_data = lv_body
          IMPORTING ev_msg = DATA(ls_updmsg) es_return = DATA(ls_return) ).
        IF ls_return-statuscode = '200'.
          "更新成功后查看是否需要重新签约
          NEW zcl_icec_agreement_api( )->get_lastagreement_info(
            EXPORTING
              iv_company       = EXACT string( ls_ztint_spechecout-companyid )
            IMPORTING
              ev_msg           =  DATA(ls_lastmsg)   " 返回参数
              es_lastagreement =  DATA(ls_lastagreement)
          ).
          IF ls_lastagreement-agreementstatus  NE 'SIGNING'. "即时生效，客户不需要签协议
            ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l(  eventid = 'apply_specheckout'
                    eventdesc = '申请特殊账期' status = 'S' message = '调用接口增加特殊账期成功'
                      key1 = iv_spno key2 = lv_code ).

            "临时额度审批抬头表
            ls_ztint_dp_pi_h-sendstatus = 'Y'."发放成功
            ls_ztint_dp_pi_h-sendmsg = '调用接口增加特殊账期成功'.

            "临时额度信息表
            ls_ztint_spechecout-msg = '申请特殊账期成功'.
            ls_ztint_spechecout-status = 'APPROVE'."已审批
            ls_ztint_spechecout-zupd_bdate = sy-datum.
            ls_ztint_spechecout-zupd_btime = sy-uzeit.
            ls_ztint_spechecout-zupd_uname = '企业微信'.
            IF ls_ztint_spechecout-originatoruserid IS NOT INITIAL.
              "通知申请人额度生效
              CLEAR lv_date_s.
              CLEAR lv_content.
              lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

              lv_content =   '开思助手 \n '   && '您申请的【特殊账期】已成功，请知悉！ \n '
               && '客户名称：' && ls_ztint_spechecout-companyname && ' \n '
               && '客户代码：' && ls_ztint_spechecout-companycode && ' \n '
               && lv_date_s.

              CLEAR ls_user_content.
              ls_user_content-content = lv_content.
              ls_user_content-userid = ls_ztint_spechecout-originatoruserid.
              ls_user_content-orderno = |{ ls_ztint_spechecout-companycode }|.
              ls_user_content-orderdesc = |{ ls_ztint_spechecout-companyname }|.
              ls_user_content-firstcatgory = '客户管理'.
              ls_user_content-secondcatgory = '客户特殊账期申请审批'.
              APPEND ls_user_content TO lt_user_content.
            ENDIF.
          ELSEIF ls_lastagreement-agreementstatus  = 'SIGNING'. "客户需要签协议 "提醒要签合同生效..

            ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_specheckout'
                      eventdesc = '申请特殊账期' status = 'S' message = '调用接口增加特殊账期成功'
                      key1 = iv_spno key2 = lv_code key3 = '需要客户签完协议后生效' ).

            "临时额度审批抬头表
            ls_ztint_dp_pi_h-sendstatus = 'Y'."发放成功
            ls_ztint_dp_pi_h-sendmsg = '调用接口增加特殊账期成功，需要客户签完协议后生效'.

            "临时额度信息表
            ls_ztint_spechecout-msg = '申请特殊账期成功，需要客户签完协议后生效'.
            ls_ztint_spechecout-status = 'APPROVE'."已审批
            ls_ztint_spechecout-zupd_bdate = sy-datum.
            ls_ztint_spechecout-zupd_btime = sy-uzeit.
            ls_ztint_spechecout-zupd_uname = '企业微信'.

            IF ls_ztint_spechecout-originatoruserid IS NOT INITIAL.
              "通知申请人额度生效
              CLEAR lv_date_s.
              CLEAR lv_content.
              lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

              lv_content =   '开思助手 \n '   && '您申请的【特殊账期】需要等客户签约协议后生效，请知悉！ \n '
               && '客户名称：' && ls_ztint_spechecout-companyname && ' \n '
               && '客户代码：' && ls_ztint_spechecout-companycode && ' \n '
               && lv_date_s.

              CLEAR ls_user_content.
              ls_user_content-content = lv_content.
              ls_user_content-userid = ls_ztint_spechecout-originatoruserid.
              ls_user_content-orderno = |{ ls_ztint_spechecout-companycode }|.
              ls_user_content-orderdesc = |{ ls_ztint_spechecout-companyname }|.
              ls_user_content-firstcatgory = '客户管理'.
              ls_user_content-secondcatgory = '客户特殊账期申请审批'.
              APPEND ls_user_content TO lt_user_content.
            ENDIF.
          ENDIF.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_specheckout'
                    eventdesc = '申请特殊账期' status = 'E' message = ls_return-message
                    key1 = iv_spno key2 = lv_code ).

          "临时额度审批抬头表
          ls_ztint_dp_pi_h-sendstatus = 'N'."发放失败
          ls_ztint_dp_pi_h-sendmsg = ls_return-message.

          "临时额度信息表
          ls_ztint_spechecout-msg = ls_return-message.
          ls_ztint_spechecout-status = 'APPROVE'."已审批
          ls_ztint_spechecout-zupd_bdate = sy-datum.
          ls_ztint_spechecout-zupd_btime = sy-uzeit.
          ls_ztint_spechecout-zupd_uname = '企业微信'.

          lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
          lv_alert_content = '开思助手 \n '  && ls_ztint_dp_pi_h-title  && ls_return-message &&  ' \n '
             && '实例号：' && ls_ztint_dp_pi_h-processinstanceid && ' \n '
             && '流程code：' && ls_ztint_dp_pi_h-processcode && ' \n '
             && lv_date_s.

          lv_fname = 'CLOSESPECHECKOUT'.
          lv_title =  '开思助手'.
          lv_msg_type = 'text'.
          CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
            EXPORTING
              iv_content = lv_alert_content
              iv_fname   = lv_fname
*             IV_URL     =
            .
*          CALL FUNCTION 'Z_FMINT_ERRORALERT'
*            IN UPDATE TASK
*            EXPORTING
*              iv_title    = lv_title
*              iv_msg_type = lv_msg_type
**             IV_URL      =
*              iv_content  = lv_alert_content
*              iv_fname    = lv_fname.
        ENDIF.

        "更新表
        MODIFY ztint_spechecout FROM ls_ztint_spechecout.
        MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.
        MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i.
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        "锁表
        CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.

        IF lt_user_content IS NOT INITIAL.
          lv_title =  '开思助手'.
          lv_msg_type = 'text'.
          CONDENSE lv_url NO-GAPS .

          CALL FUNCTION 'Z_FMQYWX_CASSINT_PUSH_MESSAGE'
            IN UPDATE TASK
            EXPORTING
              iv_title    = lv_title
              iv_msgtype = lv_msg_type
              iv_url      = lv_url
            TABLES
              it_userlist = lt_user_content.
        ENDIF.
      WHEN 'refuse'."审批拒绝
        CLEAR ls_ztint_spechecout.
        SELECT SINGLE * FROM ztint_spechecout INTO CORRESPONDING FIELDS OF ls_ztint_spechecout
          WHERE instanceid = iv_spno.
        IF sy-subrc = 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_specheckout'
                    eventdesc = '申请特殊账期' status = 'S' message = '更新特殊账期申请状态'
                    key1 = iv_spno key2 = lv_code ).

          ls_ztint_spechecout-status = 'UNAPPROVE'.
          ls_ztint_spechecout-msg = '审批拒绝'.
          ls_ztint_spechecout-zupd_bdate = sy-datum.
          ls_ztint_spechecout-zupd_btime = sy-uzeit.
          ls_ztint_spechecout-zupd_uname = '企业微信'.

          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          MODIFY ztint_spechecout FROM ls_ztint_spechecout.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_specheckout'
                    eventdesc = '申请特殊账期' status = 'E' message = '特殊账期申请拒绝未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).

          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ENDIF.
      WHEN 'terminate'."审批撤销
        CLEAR ls_ztint_spechecout.
        SELECT SINGLE * FROM ztint_spechecout INTO CORRESPONDING FIELDS OF ls_ztint_spechecout
          WHERE instanceid = iv_spno.
        IF sy-subrc = 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_specheckout'
                    eventdesc = '申请特殊账期' status = 'S' message = '更新特殊账期申请状态撤销'
                    key1 = iv_spno key2 = lv_code ).
          ls_ztint_spechecout-status = 'CANCELED'.
          ls_ztint_spechecout-msg = '已撤销'.
          ls_ztint_spechecout-zupd_bdate = sy-datum.
          ls_ztint_spechecout-zupd_btime = sy-uzeit.
          ls_ztint_spechecout-zupd_uname = '企业微信'.

          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          MODIFY ztint_spechecout FROM ls_ztint_spechecout.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'apply_specheckout'
                    eventdesc = '申请特殊账期' status = 'E' message = '特殊账期申请撤销未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).

          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ENDIF.
      WHEN OTHERS.
    ENDCASE.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->TYPECHANGE_ICEC_CLOSE
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD typechange_icec_close.

    DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h,
          ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
          lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i,
          ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
          lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l,
          ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
    DATA:lt_user_content TYPE  zsint_push_userlist_tab,
         ls_user_content TYPE  zsint_push_userlist.
    DATA:lv_body          TYPE string,
         lv_instance      TYPE zde_intprocessinstanceid,
         lv_alert_content TYPE string,
         lv_fname         TYPE string,
         lv_url           TYPE string,
         lv_title         TYPE string,
         lv_msg_type      TYPE string,
         lv_content       TYPE string,
         lv_date_s        TYPE string.

    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
    DATA(lv_code) = ls_detail-info-template_id.

    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
                           sendmsg = 'X' fname = 'BULELIST' ).
      RETURN.
    ENDIF.


    "审批结果
    CASE iv_type.
      WHEN 'agree'. "审批通过
        "查找申请记录
        SELECT SINGLE * FROM ztint_typechange INTO @DATA(ls_ztint_typechange) WHERE instanceid = @iv_spno.
        IF sy-subrc NE 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'type_change'
                    eventdesc = '白条账单转换' status = 'E' message = '未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.
        "判断是否已经申请过
        SELECT SINGLE * FROM ztint_dp_pi_h INTO CORRESPONDING FIELDS OF @ls_ztint_dp_pi_h
          WHERE processinstanceid = @iv_spno.
        IF ls_ztint_dp_pi_h-sendstatus = 'Y'. "已申请过
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'type_change'
                    eventdesc = '白条账单转换' status = 'E' message = '实例已申请过白条挂账转换'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.

        lv_instance = iv_spno.
        "锁表
        CALL FUNCTION 'ENQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.
        IF sy-subrc NE 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'type_change'
                    eventdesc = '白条账单转换' status = 'E' message = '实例申请白条挂账转换锁表失败'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          RETURN.
        ENDIF.
        "抬头信息
        ls_ztint_dp_pi_h = prepare_ztint_dp_pi_h( is_detail = ls_detail iv_type = iv_type ).
        "审批详情
        LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
          CLEAR ls_ztint_dp_pi_i.
          ls_ztint_dp_pi_i = prepare_ztint_dp_pi_i( iv_spno = iv_spno is_content = ls_content ).
          APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.
        ENDLOOP.

        "获取发起人名称
        SELECT SINGLE username INTO @DATA(lv_username) FROM ztint_user_inf
          WHERE qywxuserid = @ls_detail-info-applyer-userid.
        IF lv_username IS INITIAL.
          lv_username = '企业微信'.
        ENDIF.

        "获取额度信息
        DATA(lo_partycredit) = NEW zcl_icec_partycredit_api( ).
        lo_partycredit->get_partycredit_detail(
          EXPORTING iv_companyid = EXACT string( ls_ztint_typechange-companyid )
          IMPORTING ev_msg = DATA(ls_msg) es_creditdetail = DATA(ls_creditdetail) ).
        IF ls_msg-type EQ 'E'.
          ls_ztint_typechange-msg = '获取额度信息失败'.
        ENDIF.

        "白条账单转换
        DATA(lo_credit) = NEW zcl_icec_partycredit_api( ).
        lv_body = '{'.
        lv_body = lv_body && '"createBy":"' && lv_username && '",' .
        lv_body = lv_body && '"partyIdFrom":"10424",' .
        IF ls_ztint_typechange-applyservicetype = 'COMPANY_ACCOUNT'."白条转挂账
          lv_body = lv_body && '"maxDeadLine":"50",'.
        ELSEIF ls_ztint_typechange-applyservicetype = 'CASSLOAN'."挂账转白条
          lv_body = lv_body && '"maxDeadLine":"25",'.
        ENDIF.
        IF ls_ztint_typechange-applyservicetype = 'ACCOUNT'.
          lv_body = lv_body && '"serviceType":"COMPANY_ACCOUNT",'.
        ELSE.
          lv_body = lv_body && '"serviceType":"' && ls_ztint_typechange-applyservicetype && '",' .
        ENDIF.
        lv_body = lv_body && '"partyIdTo":"' && ls_ztint_typechange-companyid && '",' .
        lv_body = lv_body && '"userLoginId":"system",'.

        DATA: lv_limit TYPE zde_intamount.
        CLEAR lv_limit .
        lv_limit =   zcl_cassint_formatter=>convert_e_to_number( ls_creditdetail-creditlimit ) .
        lv_limit = lv_limit / 1000000.
        lv_body = lv_body && '"creditLimit":"' && lv_limit && '",' .

        lv_body = lv_body && '"creditSt":"1",'.

        IF ls_creditdetail-punishaccountrate IS NOT INITIAL.
          REPLACE ALL OCCURRENCES OF  '"' IN ls_creditdetail-punishaccountrate WITH '\"'.
          lv_body = lv_body && '"punishAccountRate":"' && ls_creditdetail-punishaccountrate && '",' .
        ENDIF.

        CLEAR lv_limit .
        lv_limit = zcl_cassint_formatter=>convert_e_to_number( ls_creditdetail-maxcreditlimit ) .
        lv_limit =  lv_limit / 1000000.
        lv_body = lv_body && '"maxCreditLimit":"' && lv_limit && '"' .
        lv_body = lv_body &&  '}'.

        DATA: ls_returned TYPE zpc_s_producttransferreturn.
        CALL METHOD lo_credit->partycredit_product_transfer
          EXPORTING
            iv_data   = lv_body
          IMPORTING
            es_return = ls_returned
*           ev_msg    =
          .
        IF ls_returned-statuscode = '200'.
          "更新成功后查看是否需要重新签约
          NEW zcl_icec_agreement_api( )->get_lastagreement_info(
            EXPORTING
              iv_company       = EXACT string( ls_ztint_typechange-companyid )
            IMPORTING
              ev_msg           =  DATA(ls_lastmsg)   " 返回参数
              es_lastagreement =  DATA(ls_lastagreement)
          ).
          IF ls_lastagreement-agreementstatus  NE 'SIGNING'. "即时生效，客户不需要签协议
            ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'type_change'
                      eventdesc = '白条账单转换' status = 'S' message = '调用接口增加白条挂账转换成功'
                      key1 = iv_spno key2 = lv_code ).
            "白条挂账转换审批抬头表
            ls_ztint_dp_pi_h-sendstatus = 'Y'."发放成功
            ls_ztint_dp_pi_h-sendmsg = '调用接口增加白条挂账转换成功'.

            "白条挂账转换信息表
            ls_ztint_typechange-msg = '申请白条挂账转换成功'.
            ls_ztint_typechange-status = 'APPROVE'."已审批
            ls_ztint_typechange-zupd_bdate = sy-datum.
            ls_ztint_typechange-zupd_btime = sy-uzeit.
            ls_ztint_typechange-zupd_uname = '企业微信'.

            IF ls_ztint_typechange-originatoruserid IS NOT INITIAL.
              "通知申请人额度生效
              CLEAR lv_date_s.
              CLEAR lv_content.
              lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.

              lv_content =   '开思助手 \n '   && '您申请的【白条挂账转换】已成功，请知悉！ \n '
               && '客户名称：' && ls_ztint_typechange-companyname && ' \n '
               && '客户代码：' && ls_ztint_typechange-companycode && ' \n '
               && lv_date_s.

              CLEAR ls_user_content.
              ls_user_content-content = lv_content.
              ls_user_content-userid = ls_ztint_typechange-originatoruserid.
              ls_user_content-orderno = |{ ls_ztint_typechange-companycode }|.
              ls_user_content-orderdesc = |{ ls_ztint_typechange-companyname }|.
              ls_user_content-firstcatgory = '客户管理'.
              ls_user_content-secondcatgory = '客户白条挂账转换申请审批'.
              APPEND ls_user_content TO lt_user_content.
            ENDIF.
          ELSEIF ls_lastagreement-agreementstatus  = 'SIGNING'. "客户需要签协议 "提醒要签合同生效.
            ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'type_change'
                      eventdesc = '白条账单转换' status = 'S' message = '调用接口增加白条挂账转换成功'
                      key1 = iv_spno key2 = lv_code key3 = '需要客户签完协议后生效' ).
            "白条挂账转换审批抬头表
            ls_ztint_dp_pi_h-sendstatus = 'Y'."发放成功
            ls_ztint_dp_pi_h-sendmsg = '调用接口增加白条挂账转换成功，需要客户签完协议后生效'.
            "白条挂账转换信息表
            ls_ztint_typechange-msg = '申请白条挂账转换成功，需要客户签约协议后生效'.
            ls_ztint_typechange-status = 'APPROVE'."已审批
            ls_ztint_typechange-zupd_bdate = sy-datum.
            ls_ztint_typechange-zupd_btime = sy-uzeit.
            ls_ztint_typechange-zupd_uname = '企业微信'.

            IF ls_ztint_typechange-originatoruserid IS NOT INITIAL.
              "通知申请人额度生效
              CLEAR lv_date_s.
              CLEAR lv_content.
              lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
              lv_content =   '开思助手 \n '   && '您申请的【白条挂账转换】需要等客户签约协议后生效，请知悉！ \n '
               && '客户名称：' && ls_ztint_typechange-companyname && ' \n '
               && '客户代码：' && ls_ztint_typechange-companycode && ' \n '
               && lv_date_s.

              CLEAR ls_user_content.
              ls_user_content-content = lv_content.
              ls_user_content-userid = ls_ztint_typechange-originatoruserid.
              ls_user_content-orderno = |{ ls_ztint_typechange-companycode }|.
              ls_user_content-orderdesc = |{ ls_ztint_typechange-companyname }|.
              ls_user_content-firstcatgory = '客户管理'.
              ls_user_content-secondcatgory = '客户白条挂账转换申请审批'.
              APPEND ls_user_content TO lt_user_content.
            ENDIF.
          ENDIF.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'type_change'
                    eventdesc = '白条账单转换' status = 'E' message = ls_returned-message
                    key1 = iv_spno key2 = lv_code  ).

          "白条挂账转换审批抬头表
          ls_ztint_dp_pi_h-sendstatus = 'N'."发放失败
          ls_ztint_dp_pi_h-sendmsg = '调用接口增加白条挂账转换失败'.

          "白条挂账转换信息表
          ls_ztint_typechange-msg = ls_returned-message.
          ls_ztint_typechange-status = 'APPROVE'."已审批
          ls_ztint_typechange-zupd_bdate = sy-datum.
          ls_ztint_typechange-zupd_btime = sy-uzeit.
          ls_ztint_typechange-zupd_uname = '企业微信'.

          CLEAR lv_date_s.
          CLEAR lv_content.
          lv_date_s = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
          lv_alert_content = '开思助手 \n '  && ls_ztint_dp_pi_h-title  && ls_returned-message && '  \n '
             && '实例号：' && ls_ztint_dp_pi_h-processinstanceid && ' \n '
             && '流程code：' && ls_ztint_dp_pi_h-processcode && ' \n '
             && lv_date_s.

          lv_fname = 'DPAPPLYTYPECHANGE'.
          lv_title =  '开思助手'.
          lv_msg_type = 'text'.

          CALL FUNCTION 'Z_FMQYWX_ALERT_ERROR_REPORT'
            EXPORTING
              iv_content = lv_alert_content
              iv_fname   = lv_fname.
        ENDIF.

        "更新表
        MODIFY ztint_typechange FROM ls_ztint_typechange.
        MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.
        MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i.
        MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        "锁表
        CALL FUNCTION 'DEQUEUE_EZ_INT_DP_PI_H'
          EXPORTING
            mode_ztint_dp_pi_h = 'E'
            mandt              = sy-mandt
            instanceid         = lv_instance.

*   推送通知
        IF lt_user_content IS NOT INITIAL.
          lv_title =  '开思助手'.
          lv_msg_type = 'text'.
          CONDENSE lv_url NO-GAPS .

          CALL FUNCTION 'Z_FMQYWX_CASSINT_PUSH_MESSAGE'
            IN UPDATE TASK
            EXPORTING
              iv_title    = lv_title
              iv_msgtype = lv_msg_type
              iv_url      = lv_url
            TABLES
              it_userlist = lt_user_content.
        ENDIF.
      WHEN 'refuse'."审批拒绝
        CLEAR ls_ztint_typechange.
        SELECT SINGLE * FROM ztint_typechange INTO CORRESPONDING FIELDS OF ls_ztint_typechange WHERE instanceid = iv_spno.
        IF sy-subrc = 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'type_change'
                    eventdesc = '白条账单转换' status = 'S' message = '更新白条挂账转换申请状态'
                    key1 = iv_spno key2 = lv_code  ).

          ls_ztint_typechange-status = 'UNAPPROVE'.
          ls_ztint_typechange-msg = '审批拒绝'.
          ls_ztint_typechange-zupd_bdate = sy-datum.
          ls_ztint_typechange-zupd_btime = sy-uzeit.
          ls_ztint_typechange-zupd_uname = '企业微信'.

          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          MODIFY ztint_typechange FROM ls_ztint_typechange.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'type_change'
                    eventdesc = '白条账单转换' status = 'E' message = '白条挂账转换申请拒绝未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).

          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ENDIF.
      WHEN 'terminate'."审批撤销
        CLEAR ls_ztint_typechange.
        SELECT SINGLE * FROM ztint_typechange INTO CORRESPONDING FIELDS OF ls_ztint_typechange WHERE instanceid = iv_spno.
        IF sy-subrc = 0.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'type_change'
                    eventdesc = '白条账单转换' status = 'S' message = '更新白条挂账转换申请状态撤销'
                    key1 = iv_spno key2 = lv_code  ).

          ls_ztint_typechange-status = 'CANCELED'.
          ls_ztint_typechange-msg = '已撤销'.
          ls_ztint_typechange-zupd_bdate = sy-datum.
          ls_ztint_typechange-zupd_btime = sy-uzeit.
          ls_ztint_typechange-zupd_uname = '企业微信'.

          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
          MODIFY ztint_typechange FROM ls_ztint_typechange.
        ELSE.
          ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'type_change'
                    eventdesc = '白条账单转换' status = 'E' message = '白条挂账转换申请撤销未找到对应的申请记录'
                    key1 = iv_spno key2 = lv_code ).
          MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
        ENDIF.
      WHEN OTHERS.
    ENDCASE.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->UPDATE_AFCLAIM_DATA
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD update_afclaim_data.

    DATA: lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h,
          ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
          lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i,
          ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
          lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l,
          ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
    DATA:lt_user_content TYPE  zsint_push_userlist_tab,
         ls_user_content TYPE  zsint_push_userlist.
    DATA:lv_body          TYPE string,
         lv_instance      TYPE zde_intprocessinstanceid,
         lv_alert_content TYPE string,
         lv_fname         TYPE string,
         lv_url           TYPE string,
         lv_title         TYPE string,
         lv_msg_type      TYPE string,
         lv_content       TYPE string,
         lv_date_s        TYPE string.

    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
    DATA(lv_code) = ls_detail-info-template_id.

    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
                           sendmsg = 'X' fname = 'BULELIST' ).
      RETURN.
    ENDIF.


    "审批结果
    DATA:lv_status TYPE ztint_afclaim-status.
    CASE iv_type.
      WHEN 'agree'.
        lv_status = 'APPROVE'.
      WHEN 'refuse'.
        lv_status = 'UNAPPROVE'.
      WHEN 'terminate'.
        lv_status = 'CANCELED'.
      WHEN OTHERS.
    ENDCASE.


    lv_instance = iv_spno.

    SELECT SINGLE guid INTO @DATA(l_guid) FROM ztint_dp_inf WHERE instanceid = @lv_instance.
    CHECK sy-subrc EQ 0.
    "锁表
    CALL FUNCTION 'ENQUEUE_EZ_INT_DP_INF'
      EXPORTING
        mode_ztint_dp_inf = 'E'
        mandt             = sy-mandt
        guid              = l_guid.

    IF sy-subrc <> 0.
      ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'afclaim_update'
                eventdesc = '售后审批流状态更新' status = 'E' message = '更新审批状态锁表失败'
                key1 = iv_spno key2 = lv_code ).
      MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
      RETURN.
    ENDIF.

    SELECT SINGLE * FROM ztint_afclaim INTO @DATA(ls_claim) WHERE guid = @l_guid.
    IF sy-subrc NE 0.
      UPDATE ztint_dp_inf
         SET status = lv_status
             msg = 'finish'
             zupd_bdate = sy-datum
             zupd_btime = sy-uzeit
             zupd_uname = sy-uname
        WHERE guid = l_guid.
    ELSE.
      DATA:ls_parts_info TYPE ztpms_parts_info,
           lt_parts_info TYPE STANDARD TABLE OF ztpms_parts_info,
           lv_businessid TYPE ztint_afclaim-businessid.

      IF iv_spno IS NOT INITIAL AND iv_type EQ 'agree' AND
         ( ls_claim-goodsto EQ '1' OR ls_claim-goodsto EQ '2' ). " OR ls_claim-goodsto EQ '7' ). 7在创建审批时就已经写入了

        SELECT * FROM ztint_afclaim_i INTO TABLE @DATA(lt_afclaim_i) WHERE guid = @l_guid.
        IF sy-subrc EQ 0.
          SELECT SINGLE requestno INTO @DATA(ls_no) FROM ztpms_parts_info WHERE requestno = @iv_spno.
          IF sy-subrc NE 0.
            LOOP AT lt_afclaim_i INTO DATA(ls_afclaim_i).
              ls_parts_info-requestno = iv_spno.
              ls_parts_info-orderid = ls_claim-orderid.
              SELECT SINGLE createdate INTO ls_parts_info-orderdate FROM zticec_order_h WHERE orderid = ls_claim-orderid.
              ls_parts_info-requestdate = ls_claim-applydate.
              ls_parts_info-requestreason = ls_claim-applyreasondesc.
              ls_parts_info-zoneid = ls_claim-cuszoneid.
              SELECT SINGLE cusid FROM ztint_cus_inf INTO ls_parts_info-cusid WHERE companycode = ls_claim-companycode.
              ls_parts_info-companycode = ls_claim-companycode.
              ls_parts_info-companyid = ls_claim-companyid.
              ls_parts_info-cusname = ls_claim-companyname.
              SELECT SINGLE venid FROM ztint_ven_inf INTO ls_parts_info-venid WHERE productstoreid = ls_claim-productstoreid.
              ls_parts_info-venname = ls_claim-productstorename.
              ls_parts_info-productstoreid = ls_claim-productstoreid.

              SELECT SINGLE productid internalnum
                FROM zticec_order_i INTO (ls_parts_info-productid,ls_parts_info-internalnum)
                WHERE orderid = ls_afclaim_i-orderid AND orderitemseqid = ls_afclaim_i-orderitemseqid.
              ls_parts_info-orderitemseqid = ls_afclaim_i-orderitemseqid.
              ls_parts_info-partsnum = ls_afclaim_i-partsnum.
              ls_parts_info-brandid = ls_afclaim_i-brandid.
              ls_parts_info-brandname = ls_afclaim_i-brandname.
              ls_parts_info-partsname = ls_afclaim_i-partsname.
              ls_parts_info-quantity = ls_afclaim_i-quantity.
              ls_parts_info-quanlity = ls_afclaim_i-quanlity.
              ls_parts_info-quanlitydesc = ls_afclaim_i-quanlitydesc.
              ls_parts_info-acturalprice = ls_afclaim_i-price.
              ls_parts_info-acturalamount = ls_afclaim_i-quantity * ls_afclaim_i-price.
              ls_parts_info-uname = ls_claim-dingusername.
              ls_parts_info-preidentify = ls_afclaim_i-preidentify.
              ls_parts_info-preidentifydesc = ls_afclaim_i-preidentifydesc.
              ls_parts_info-status = '1'.
              ls_parts_info-zcrt_bdate = sy-datum.
              ls_parts_info-zcrt_btime = sy-uzeit.
              ls_parts_info-zcrt_uname = sy-uname.
              APPEND ls_parts_info TO lt_parts_info.
              CLEAR:ls_parts_info,ls_afclaim_i.
            ENDLOOP.
            MODIFY ztpms_parts_info FROM TABLE lt_parts_info.
          ENDIF.
        ENDIF.
      ELSEIF iv_spno IS NOT INITIAL AND ( iv_type EQ 'refuse'  OR   "拒绝或者撤回审批时，将7的状态改为9
          iv_type EQ 'terminate' ).
        IF ( ls_claim-applyreason EQ '8' OR ls_claim-applyreason EQ '9' ) AND ls_claim-goodsto EQ '7' .
          SELECT SINGLE status INTO @DATA(lv_afstatus) FROM ztpms_parts_info WHERE requestno = @iv_spno AND status EQ '3'.
          IF sy-subrc NE 0 ."没有入库的
            UPDATE ztpms_parts_info
              SET status = '9'
                  zupd_uname = sy-uname
                  zupd_bdate = sy-datum
                  zupd_btime = sy-uzeit
            WHERE requestno = iv_spno.
          ENDIF.
        ENDIF.

        IF ls_claim-woid IS NOT INITIAL.
          UPDATE ztint_wo_note SET afclaimstaus = lv_status
                                   zupd_bdate = sy-datum zupd_btime = sy-uzeit zupd_uname = sy-uname
          WHERE woid = ls_claim-woid AND processcode = lv_instance.
        ENDIF.
      ENDIF.

      UPDATE ztint_dp_inf
         SET status = lv_status
             msg = 'finish'
             zupd_bdate = sy-datum
             zupd_btime = sy-uzeit
             zupd_uname = sy-uname
        WHERE guid = l_guid.

      UPDATE ztint_afclaim
         SET status = lv_status
             msg = 'finish'
             businessid = iv_spno
             zupd_bdate = sy-datum
             zupd_btime = sy-uzeit
             zupd_uname = sy-uname
        WHERE guid = l_guid.

      IF iv_spno IS NOT INITIAL AND iv_type EQ 'agree' AND ls_claim-goodsto NE '3'.
        SELECT SINGLE deptid INTO @DATA(lv_deptid) FROM ztmm_1chejian_sk
          WHERE fname = 'MMAPI' AND deptid = @ls_claim-goodsto.
        IF sy-subrc EQ 0.
          lv_businessid = iv_spno.
          CALL FUNCTION 'Z_FMMM_1CHEJIAN_SHJ_PROCESS'
            STARTING NEW TASK 'T1'
            EXPORTING
              iv_type       = 'REQUEST'
              iv_businessid = lv_businessid
*             IV_WOID       =
            .
        ENDIF.
      ENDIF.
    ENDIF.

    CALL FUNCTION 'DEQUEUE_EZ_INT_DP_INF'
      EXPORTING
        mode_ztint_dp_inf = 'E'
        mandt             = sy-mandt
        guid              = l_guid.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->UPDATE_CUSTOMER_VOLUMECLASS
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD update_customer_volumeclass.

    DATA:lv_timestamp TYPE p.
    DATA:lv_lastdate TYPE sy-datum.
    DATA:lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l,
         ls_ztint_dp_pi_l TYPE ztint_dp_pi_l.
    DATA:lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h,
         ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
         lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i,
         ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i.
**
    DATA:lv_companycode TYPE string,
         lv_companyname TYPE string,
         lv_originclass TYPE string,
         lv_volume      TYPE string,
         lv_remark      TYPE string,
         lv_newclass    TYPE string,
         lv_finishstamp TYPE string.
    DATA:ls_ztint_opera_log TYPE ztint_opera_log,
         lt_ztint_opera_log LIKE STANDARD TABLE OF  ls_ztint_opera_log.
    DATA:ls_cvolumeadj TYPE ztint_cvolumeadj.
    DEFINE get_opera_log.
      try.
      ls_ztint_opera_log-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
      CATCH cx_uuid_error INTO DATA(lo_err).
        ENDTRY.
      ls_ztint_opera_log-userid = &1."操作人员
      ls_ztint_opera_log-username = &2.
      ls_ztint_opera_log-operatedate = sy-datum."操作时间
      ls_ztint_opera_log-operatetime = sy-uzeit.
      ls_ztint_opera_log-fnam = &3. "所属模块
      ls_ztint_opera_log-operatetion = &4. "所属操作
      ls_ztint_opera_log-objectid = &5."操作对象
      ls_ztint_opera_log-object = &6.
      ls_ztint_opera_log-content = &7."内容
      append ls_ztint_opera_log to lt_ztint_opera_log.
      CLEAR ls_ztint_opera_log.
    END-OF-DEFINITION.


    DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
    DATA(lv_code) = ls_detail-info-template_id.
    IF ls_detail-errcode NE '0'.
      "没找到 记录日志
      log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
                           zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
                           sendmsg = 'X' fname = 'VOLUMECLASS' ).
      RETURN.
    ENDIF.

    CASE iv_type.
      WHEN 'agree'.
        "判断这个实例是否已经加过
        "实例已发放记录日志
        SELECT SINGLE * FROM ztint_dp_pi_h INTO CORRESPONDING FIELDS OF @ls_ztint_dp_pi_h
          WHERE processinstanceid =  @iv_spno.
        IF ls_ztint_dp_pi_h-sendstatus = 'Y'. "已发放
          "已发放记录日志
          log_ztint_dp_pi_log( zeventid = 'send_compe_already' zevent = '客户等级调整申请'
                             zmsg = '客户等级调整申请已处理' zvalue1 = iv_spno zvalue2 = lv_code ).
          RETURN.
        ENDIF.

        "取配置的流程字段
        SELECT * FROM ztint_dp_field INTO TABLE @DATA(lt_ztint_dp_field) WHERE processcode = @lv_code.
        SORT lt_ztint_dp_field BY dingname.

        "取配置的流程字段值
        SELECT * FROM ztint_dp_value INTO TABLE @DATA(lt_ztint_dp_value) WHERE processcode = @lv_code.

        "没增加过
        "抬头信息赋值
        ls_ztint_dp_pi_h = prepare_ztint_dp_pi_h( is_detail = ls_detail iv_type = iv_type ).
        "审批详情赋值
        LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
          CLEAR ls_ztint_dp_pi_i.
          ls_ztint_dp_pi_i = prepare_ztint_dp_pi_i( is_content = ls_content iv_spno = iv_spno ).
          APPEND ls_ztint_dp_pi_i TO lt_ztint_dp_pi_i.

          READ TABLE lt_ztint_dp_field INTO DATA(ls_ztint_dp_field) WITH KEY dingname = ls_ztint_dp_pi_i-name BINARY SEARCH.
          IF sy-subrc = 0.
            CASE ls_ztint_dp_field-name.
              WHEN 'companycode'. "维修厂代码
                lv_companycode = ls_ztint_dp_pi_i-value.
              WHEN 'companyname'."维修厂名称
                lv_companyname = ls_ztint_dp_pi_i-value.
              WHEN 'originclass'."原等级
                lv_originclass = ls_ztint_dp_pi_i-value.
              WHEN 'newclass'."调整后等级
                lv_newclass = ls_ztint_dp_pi_i-value.
              WHEN 'volume'."体量
                lv_volume = ls_ztint_dp_pi_i-value.
                CONDENSE lv_volume.
              WHEN 'remark'."调整原因
                lv_remark = ls_ztint_dp_pi_i-value.
              WHEN OTHERS.
            ENDCASE.
          ENDIF.
          CLEAR ls_ztint_dp_field.
        ENDLOOP.

        IF lv_companycode IS NOT INITIAL.
          SELECT SINGLE cusid,cusname INTO @DATA(ls_cusinf) FROM ztint_cus_inf WHERE companycode = @lv_companycode.
        ENDIF.
        IF lv_companycode IS INITIAL OR ls_cusinf-cusid IS INITIAL.
          "没找到相应的客户
          log_ztint_dp_pi_log( zeventid = 'nocompanycode' zevent = '获取客户主数据'
                               zmsg = '获取客户主数据失败' zvalue1 = iv_spno  zvalue2 = lv_code
                               sendmsg = 'X' fname = 'VOLUMECLASS' ).
          MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.
          MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i.
          RETURN.
        ENDIF.

        ls_ztint_dp_pi_h-sendstatus = 'Y'.

        SELECT SINGLE userid,username INTO @DATA(ls_opera_per) FROM ztint_user_inf
                  WHERE qywxuserid = @ls_detail-info-applyer-userid.
        TRY .
            DATA(lv_adjguid) = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
          CATCH cx_uuid_error.
        ENDTRY.
        ls_cvolumeadj-guid = lv_adjguid.
        ls_cvolumeadj-cusid = ls_cusinf-cusid.
        ls_cvolumeadj-companycode = lv_companycode.
        ls_cvolumeadj-cusname = ls_cusinf-cusname.
        ls_cvolumeadj-originclass = lv_originclass.
        ls_cvolumeadj-newclass = lv_newclass.
        ls_cvolumeadj-volume = lv_volume.
        ls_cvolumeadj-remark = lv_remark.
        ls_cvolumeadj-dinguserid = ls_detail-info-applyer-userid.
        ls_cvolumeadj-username = ls_opera_per-username.
        ls_cvolumeadj-instanceid = iv_spno.
        ls_cvolumeadj-processcode = lv_code.
*        ls_cvolumeadj-businessid = ls_detail-business_id.
        ls_cvolumeadj-status = 'APPROVE'.
        ls_cvolumeadj-agreedate = sy-datum.
        ls_cvolumeadj-agreetime = sy-uzeit.
        "日志
*      *下个月第一天
        CALL FUNCTION 'FIMA_DATE_CREATE'
          EXPORTING
            i_date                  = sy-datum
            i_months                = '+1'
            i_set_last_day_of_month = 'X'
          IMPORTING
            e_date                  = lv_lastdate.
        DATA(lv_month) = lv_lastdate+4(2).
        SELECT SINGLE value INTO @DATA(lv_adjvolumedate) FROM ztint_par WHERE fname = 'ADJVOLUMEDATE'.
        SELECT * FROM dd07t INTO TABLE @DATA(lt_dd07t) WHERE domname = 'ZDM_VOLUMECLASS'.
        DATA(lv_volumedesc_old) = VALUE #( lt_dd07t[ domvalue_l = lv_originclass ]-ddtext OPTIONAL ).
        DATA(lv_volumedesc_new) = VALUE #( lt_dd07t[ domvalue_l = lv_newclass ]-ddtext OPTIONAL ).
        DATA(lv_content_opera) = |{ ls_opera_per-username }提交的体量分类调整审批通过，客户体量分类将于{ lv_month }月{ lv_adjvolumedate }日从{ lv_volumedesc_old }调整为{ lv_volumedesc_new }，预估体量为 { ls_cvolumeadj-volume }万 |.
        get_opera_log ls_opera_per-userid ls_opera_per-username  'M001'  'O002' ls_cusinf-cusid ls_cusinf-cusname lv_content_opera.
        IF ls_cvolumeadj IS NOT INITIAL.
          MODIFY ztint_cvolumeadj FROM ls_cvolumeadj.
        ENDIF.
        MODIFY ztint_opera_log FROM TABLE lt_ztint_opera_log.

        MODIFY ztint_dp_pi_h FROM ls_ztint_dp_pi_h.
        MODIFY ztint_dp_pi_i FROM TABLE lt_ztint_dp_pi_i.
      WHEN 'refuse' OR 'terminate'.
      WHEN OTHERS.
    ENDCASE.
  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CORPWEIXIN_APPROVAL_API->UPDATE_REPAIRCLAIM_DATA
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_SPNO                        TYPE        STRING
* | [--->] IV_TYPE                        TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
METHOD update_repairclaim_data.

  DATA: ls_dp_inf        TYPE ztint_dp_inf,
        ls_reclaim       TYPE ztint_reclaim,
        lt_ztint_dp_pi_h TYPE STANDARD TABLE OF ztint_dp_pi_h,
        ls_ztint_dp_pi_h LIKE LINE OF lt_ztint_dp_pi_h,
        lt_ztint_dp_pi_i TYPE STANDARD TABLE OF ztint_dp_pi_i,
        ls_ztint_dp_pi_i LIKE LINE OF lt_ztint_dp_pi_i,
        lt_ztint_dp_pi_l TYPE STANDARD TABLE OF ztint_dp_pi_l,
        ls_ztint_dp_pi_l LIKE LINE OF lt_ztint_dp_pi_l.
  DATA:lt_user_content TYPE  zsint_push_userlist_tab,
       ls_user_content TYPE  zsint_push_userlist.
  DATA:lv_body          TYPE string,
       lv_instance      TYPE zde_intprocessinstanceid,
       lv_alert_content TYPE string,
       lv_fname         TYPE string,
       lv_url           TYPE string,
       lv_title         TYPE string,
       lv_msg_type      TYPE string,
       lv_content       TYPE string,
       lv_date_s        TYPE string.

  DATA(ls_detail) = get_approval_detail_by_spno( spno = iv_spno ).
  DATA(lv_code) = ls_detail-info-template_id.
  DATA(lo_merchant) = NEW zcl_icec_merchant_api( ).

  IF ls_detail-errcode NE '0'.
    "没找到 记录日志
    log_ztint_dp_pi_log( zeventid = 'get_processinstance_detail' zevent = '获取企业微信审批实例'
    zmsg = '获取企业微信审批实例失败' zvalue1 = iv_spno  zvalue2 = lv_code
    sendmsg = 'X' fname = 'BULELIST' ).
    RETURN.
  ENDIF.


  "审批结果
  DATA:lv_status TYPE ztint_reclaim-status.
  CASE iv_type.
    WHEN 'agree'.
      lv_status = 'APPROVE'.
    WHEN 'refuse'.
      lv_status = 'UNAPPROVE'.
    WHEN 'terminate'.
      lv_status = 'CANCELED'.
    WHEN OTHERS.
  ENDCASE.


  lv_instance = iv_spno.

  SELECT SINGLE guid INTO @DATA(l_guid) FROM ztint_dp_inf WHERE instanceid = @lv_instance.

*  CHECK sy-subrc EQ 0.
  IF sy-subrc NE 0.

    "给表里塞数
    TRY .
        ls_dp_inf-guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
      CATCH cx_uuid_error.
    ENDTRY.

    ls_dp_inf-dptype = 'REPAIRCLAIMOC'.
    SELECT SINGLE * INTO @DATA(ls_reuser) FROM ztint_re_user. "配置的审批人虚拟账号信息
    ls_dp_inf-userid = ls_reuser-userid.
    ls_dp_inf-deptid = ls_reuser-deptid.
*  ls_dp_inf-companycode =
*  ls_dp_inf-companyname =
    ls_dp_inf-instanceid = iv_spno.
    SELECT SINGLE processcode INTO @ls_dp_inf-processcode FROM ztint_dp_code WHERE fname = @ls_dp_inf-dptype.

*  ls_dp_inf-businessid =
    ls_dp_inf-status = 'SUBMIT'.
*  ls_dp_inf-msg =
*  ls_dp_inf-remark =
    ls_dp_inf-zcrt_uname = ls_reuser-username.
    ls_dp_inf-zcrt_bdate = sy-datum.
    ls_dp_inf-zcrt_btime = sy-uzeit.

    MODIFY ztint_dp_inf FROM ls_dp_inf.

    ls_reclaim-guid = ls_dp_inf-guid.
    ls_reclaim-deptid = ls_reuser-deptid.
    ls_reclaim-deptname = ls_reuser-deptname.
*ls_reclaim-applyuserid =
    "获取返修赔信息
    LOOP AT ls_detail-info-apply_data-contents INTO DATA(ls_content).
      READ TABLE ls_content-title INTO DATA(ls_title) INDEX 1.
      IF ls_title-text = '申请人'.
        ls_reclaim-applyusername = ls_content-value-text.
      ELSEIF ls_title-text = '销售订单'.
        ls_reclaim-orderid = ls_content-value-text.
      ELSEIF ls_title-text = '销售配件'.

      ELSEIF ls_title-text = '关联售后单'.
        ls_reclaim-asorderid = ls_content-value-text.
      ELSEIF ls_title-text = '关联故障单'.
        ls_reclaim-issueorderid = ls_content-value-text.
      ELSEIF ls_title-text = '故障原因'.
        ls_reclaim-issuereason = ls_content-value-text.
      ELSEIF ls_title-text = '故障描述'.
        ls_reclaim-issueremark = ls_content-value-text.
      ELSEIF ls_title-text = '赔付给谁'.
        ls_reclaim-applyto = ls_content-value-text.
      ELSEIF ls_title-text = '赔付方式'.
        ls_reclaim-applyway = ls_content-value-text.
      ELSEIF ls_title-text = '赔付金额'.
        ls_reclaim-applyamt = ls_content-value-text.
      ELSEIF ls_title-text = '赔付数量'.
        ls_reclaim-quantity = ls_content-value-new_number.
      ELSEIF ls_title-text = '附件url'.

      ENDIF.
    ENDLOOP.

    ls_reclaim-dinguserid = ls_reuser-dinguserid.
    ls_reclaim-dingusername = ls_reuser-username.
    ls_reclaim-instanceid = iv_spno.
    ls_reclaim-processcode = ls_dp_inf-processcode.
    ls_reclaim-status  = 'SUBMIT'.
    ls_reclaim-zcrt_uname = ls_reuser-username.
    ls_reclaim-zcrt_bdate = sy-datum.
    ls_reclaim-zcrt_btime = sy-uzeit.
    ls_reclaim-zupd_uname = ls_reuser-username.
    ls_reclaim-zupd_bdate = sy-datum.
    ls_reclaim-zupd_btime = sy-uzeit.

    MODIFY ztint_reclaim FROM ls_reclaim.

    l_guid = ls_dp_inf-guid.

  ENDIF.
  "锁表
  CALL FUNCTION 'ENQUEUE_EZ_INT_DP_INF'
    EXPORTING
      mode_ztint_dp_inf = 'E'
      mandt             = sy-mandt
      guid              = l_guid.

  IF sy-subrc <> 0.
    ls_ztint_dp_pi_l = prepare_ztint_dp_pi_l( eventid = 'afclaim_update'
    eventdesc = '返修赔审批流状态更新' status = 'E' message = '更新审批状态锁表失败'
    key1 = iv_spno key2 = lv_code ).
    MODIFY ztint_dp_pi_l FROM ls_ztint_dp_pi_l.
    RETURN.
  ENDIF.

  SELECT SINGLE * FROM ztint_reclaim INTO @DATA(ls_claim) WHERE guid = @l_guid.
  IF sy-subrc NE 0.
    UPDATE ztint_dp_inf
    SET status = lv_status
    msg = 'finish'
    zupd_bdate = sy-datum
    zupd_btime = sy-uzeit
    zupd_uname = sy-uname
    WHERE guid = l_guid.
  ELSE.


    UPDATE ztint_dp_inf
    SET status = lv_status
    msg = 'finish'
    zupd_bdate = sy-datum
    zupd_btime = sy-uzeit
    zupd_uname = sy-uname
    WHERE guid = l_guid.

    UPDATE ztint_reclaim
    SET status = lv_status
    msg = 'finish'
    businessid = iv_spno
    zupd_bdate = sy-datum
    zupd_btime = sy-uzeit
    zupd_uname = sy-uname
    WHERE guid = l_guid.


  ENDIF.

  CALL FUNCTION 'DEQUEUE_EZ_INT_DP_INF'
    EXPORTING
      mode_ztint_dp_inf = 'E'
      mandt             = sy-mandt
      guid              = l_guid.
  "回调金融品台
  "审批结果
  DATA:lv_userloginid   TYPE string,
       lv_userloginname TYPE string,
       lv_reason        TYPE string.
  LOOP AT ls_detail-info-sp_record INTO DATA(ls_record).
    LOOP AT ls_record-details INTO DATA(ls_recorddetail) WHERE sp_status = ls_record-sp_status.
      lv_userloginid = ls_recorddetail-approver-userid.
      SELECT SINGLE username INTO lv_userloginname FROM ztint_user_inf WHERE dinguserid = lv_userloginid AND isstill = 'X'.
      lv_reason = ls_recorddetail-speech.
    ENDLOOP.
  ENDLOOP.
  CASE iv_type.
    WHEN 'agree'.
      lv_body = '{' && |"oaNo": "{ iv_spno }","userLoginId": "{ lv_userloginid }","userLoginName": "{ lv_userloginname }"| && '}'.
      lo_merchant->repair_compensation_complete(
      EXPORTING
        iv_body = lv_body
      IMPORTING
      ev_msg  = DATA(ev_msg)
      es_data = DATA(es_return) ).
    WHEN 'refuse'.
      "返修赔信息获取
      IF ls_reclaim IS INITIAL.
        SELECT SINGLE * INTO ls_reclaim FROM ztint_reclaim WHERE instanceid = lv_instance.
      ENDIF.

      "拒绝后给拓展、客户经理、审批发起人发消息
      DATA: lv_wxcontent TYPE string,
            lv_log_msg   TYPE string.
      DATA: lv_date TYPE sy-datum,
            lv_time TYPE sy-uzeit.
      lv_date = sy-datum.
      lv_time = sy-uzeit.

      "获取拓展经理
      SELECT SINGLE a~productstoreid,a~custcompanycode,a~custcompanyname,b~displayname,c~userid INTO @DATA(ls_orderinf) FROM zticec_order_h AS a
            LEFT JOIN ztint_ven_inf AS b
            ON a~productstoreid = b~productstoreid
            LEFT JOIN ztint_ven_user AS c
            ON b~venid = c~venid
            AND c~usertype = '1'
            AND c~ispre = ''
            AND c~isdelete = ''
            WHERE a~orderid = @ls_reclaim-orderid.
      IF sy-subrc = 0.

        lv_wxcontent = |<div CLASS=\\"gray\\">{ lv_date+0(4) }年{ lv_date+4(2) }月{ lv_date+6(2) }日 { lv_time TIME = ISO }</div>| &&
        |<div CLASS=\\"highlight\\">您有一条【严选返修赔申请】被拒绝，请关注，谢谢!</div>| &&
        |<div>销售订单：{ ls_reclaim-orderid }</div>| &&
        |<div>配件信息：{ ls_reclaim-partsnum }-{ ls_reclaim-partsname }</div>| &&
        |<div>关联故障单：{ ls_reclaim-issueorderid }</div>| &&
        |<div>故障原因：{ ls_reclaim-issuereason }</div>| &&
        |<div>故障描述：{ ls_reclaim-issueremark }</div>| &&
        |<div>店铺代码：{ ls_orderinf-productstoreid }</div>| &&
        |<div>店铺名称：{ ls_orderinf-displayname }</div>| &&
        |<div>维修厂代码：{ ls_orderinf-custcompanycode }</div>| &&
        |<div>维修厂名称：{ ls_orderinf-custcompanyname }</div>| &&
        |<div>预计赔付金币：{ ls_reclaim-applyamt }</div>|.
        ls_user_content-userid = ls_orderinf-userid.
        ls_user_content-orderno = ls_reclaim-issueorderid.
        ls_user_content-orderdesc = '故障单号'.
        ls_user_content-firstcatgory = '返修赔审批'.
        ls_user_content-secondcatgory = '返修赔审批消息提醒'.
        ls_user_content-wxcontent = lv_wxcontent.
        APPEND ls_user_content TO lt_user_content.
        CLEAR:ls_user_content.
      ENDIF.

      "获取客户经理
      SELECT SINGLE a~custcompanycode,b~cusid,c~userid,c~usertype INTO @DATA(ls_cususer)
            FROM zticec_order_h AS a
            LEFT JOIN ztint_cus_inf AS b
            ON a~custcompanycode = b~companycode
            LEFT JOIN ztint_cus_user AS c
            ON b~cusid = c~cusid
            AND c~usertype = '1'
            AND c~isdelete = ''
            AND c~ispre = ''
            WHERE a~orderid = @ls_reclaim-orderid.
      IF sy-subrc = 0.

        lv_wxcontent = |<div CLASS=\\"gray\\">{ lv_date+0(4) }年{ lv_date+4(2) }月{ lv_date+6(2) }日 { lv_time TIME = ISO }</div>| &&
        |<div CLASS=\\"highlight\\">您有一条【严选返修赔申请】被拒绝，请关注，谢谢!</div>| &&
        |<div>销售订单：{ ls_reclaim-orderid }</div>| &&
        |<div>配件信息：{ ls_reclaim-partsnum }-{ ls_reclaim-partsname }</div>| &&
        |<div>关联故障单：{ ls_reclaim-issueorderid }</div>| &&
        |<div>故障原因：{ ls_reclaim-issuereason }</div>| &&
        |<div>故障描述：{ ls_reclaim-issueremark }</div>| &&
        |<div>店铺代码：{ ls_orderinf-productstoreid }</div>| &&
        |<div>店铺名称：{ ls_orderinf-displayname }</div>| &&
        |<div>维修厂代码：{ ls_orderinf-custcompanycode }</div>| &&
        |<div>维修厂名称：{ ls_orderinf-custcompanyname }</div>| &&
        |<div>预计赔付金币：{ ls_reclaim-applyamt }</div>|.
        ls_user_content-userid = ls_cususer-userid.
        ls_user_content-orderno = ls_reclaim-issueorderid.
        ls_user_content-orderdesc = '故障单号'.
        ls_user_content-firstcatgory = '返修赔审批'.
        ls_user_content-secondcatgory = '返修赔审批消息提醒'.
        ls_user_content-wxcontent = lv_wxcontent.
        APPEND ls_user_content TO lt_user_content.
        CLEAR:ls_user_content.
      ENDIF.

      "获取审批发起人
      "先按申请人名称获取企微对应的人，获取不到就发给发券人
      SELECT SINGLE userid INTO @DATA(lv_userid)
            FROM ztint_user_inf
            WHERE username = @ls_reclaim-applyusername
            AND isstill = 'X'.
      IF sy-subrc NE 0.
        IF ls_reclaim-applyusername CA '(' .
          SPLIT ls_reclaim-applyusername AT '(' INTO DATA(lv_applyusername) DATA(lv_applyusername1).
        ELSE.
          SPLIT ls_reclaim-applyusername AT '（' INTO lv_applyusername lv_applyusername1.
        ENDIF.
        SELECT SINGLE userid INTO @lv_userid"因为存在类似这种：黄凯平(开思售出运营)
        FROM ztint_user_inf
        WHERE username = @lv_applyusername
        AND isstill = 'X'.
        IF sy-subrc NE 0.
          SELECT SINGLE userid INTO @lv_userid
          FROM ztint_user_inf
          WHERE qywxuserid = @ls_detail-info-applyer-userid
          AND isstill = 'X'.
        ENDIF.
      ENDIF.
      IF lv_userid IS NOT INITIAL.
        lv_wxcontent = |<div CLASS=\\"gray\\">{ lv_date+0(4) }年{ lv_date+4(2) }月{ lv_date+6(2) }日 { lv_time TIME = ISO }</div>| &&
        |<div CLASS=\\"highlight\\">您有一条【严选返修赔申请】被拒绝，请关注，谢谢!</div>| &&
        |<div>销售订单：{ ls_reclaim-orderid }</div>| &&
        |<div>配件信息：{ ls_reclaim-partsnum }-{ ls_reclaim-partsname }</div>| &&
        |<div>关联故障单：{ ls_reclaim-issueorderid }</div>| &&
        |<div>故障原因：{ ls_reclaim-issuereason }</div>| &&
        |<div>故障描述：{ ls_reclaim-issueremark }</div>| &&
        |<div>店铺代码：{ ls_orderinf-productstoreid }</div>| &&
        |<div>店铺名称：{ ls_orderinf-displayname }</div>| &&
        |<div>维修厂代码：{ ls_orderinf-custcompanycode }</div>| &&
        |<div>维修厂名称：{ ls_orderinf-custcompanyname }</div>| &&
        |<div>预计赔付金币：{ ls_reclaim-applyamt }</div>|.
        ls_user_content-userid = lv_userid.
        ls_user_content-orderno = ls_reclaim-issueorderid.
        ls_user_content-orderdesc = '故障单号'.
        ls_user_content-firstcatgory = '返修赔审批'.
        ls_user_content-secondcatgory = '返修赔审批消息提醒'.
        ls_user_content-wxcontent = lv_wxcontent.
        APPEND ls_user_content TO lt_user_content.
        CLEAR:ls_user_content.
      ENDIF.

      "发消息
      IF lt_user_content IS NOT INITIAL.
        lv_title =  '开思助手'.
        lv_msg_type = 'text'.
        CALL FUNCTION 'Z_FMINT_WO_PUSH'
          IN UPDATE TASK
          EXPORTING
            iv_title    = lv_title
            iv_msg_type = lv_msg_type
            iv_log_msg  = lv_log_msg
          TABLES
            it_userlist = lt_user_content.
      ENDIF.
      COMMIT WORK AND WAIT.

      lv_body = '{' && |"oaNo": "{ iv_spno }","reason": "{ lv_reason }","troubleTicketNo": "","userLoginId": "{ lv_userloginid }","userLoginName": "{ lv_userloginname }"| && '}'.
      lo_merchant->repair_compensation_reject(
      EXPORTING
        iv_body = lv_body
      IMPORTING
        ev_msg  = ev_msg
        es_data = es_return ).

    WHEN 'terminate'.
    WHEN OTHERS.
  ENDCASE.



ENDMETHOD.
ENDCLASS.