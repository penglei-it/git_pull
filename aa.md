
  TYPES: BEGIN OF ty_userno,
           userno TYPE ztint_user_inf-userno,
         END OF ty_userno.
  TYPES:BEGIN OF ty_save_custag,
          businesstype  TYPE string,
          conditiontype TYPE string,
          companyid     TYPE string,
          externalid    TYPE string,
          number        TYPE string,
          operator      TYPE string,
        END OF ty_save_custag.
  DATA ls_custag TYPE ty_save_custag.
  DATA(lo_user) = NEW zcl_icec_user_api( ).
  DATA lv_statusid TYPE string.
  DATA lv_username TYPE string.
  DATA lv_asid TYPE string.
  DATA lv_orderno TYPE char50.
  DATA lv_reason TYPE string.
  DATA ls_wo_header TYPE ztint_wo_header.
  DATA(lo_order_api) = NEW zcl_icec_order_api( ).
  DATA lt_wo_item TYPE STANDARD TABLE OF ztint_wo_item.
  DATA ls_wo_item LIKE LINE OF lt_wo_item.
  DATA lt_ztint_wo_flow TYPE STANDARD TABLE OF ztint_wo_flow.
  DATA ls_ztint_wo_flow LIKE LINE OF lt_ztint_wo_flow.
  DATA ls_nextflow LIKE LINE OF lt_ztint_wo_flow.
  DATA lt_ztint_wo_note    TYPE STANDARD TABLE OF ztint_wo_note.
  DATA ls_ztint_wo_note    LIKE LINE OF lt_ztint_wo_note.
  DATA lt_ztint_wo_log  TYPE STANDARD TABLE OF ztint_wo_log.
  DATA ls_ztint_wo_log LIKE LINE OF lt_ztint_wo_log.
  DATA lt_ztint_wo_file TYPE STANDARD TABLE OF ztint_wo_file.
  DATA ls_ztint_wo_file LIKE LINE OF lt_ztint_wo_file.
  DATA lt_ztint_wo_nouser TYPE STANDARD TABLE OF ztint_wo_nouser.
  DATA ls_ztint_wo_nouser  LIKE LINE OF lt_ztint_wo_nouser.
  DATA lt_ztint_wo_fl TYPE STANDARD TABLE OF ztint_wo_fl.
  DATA ls_ztint_wo_fl LIKE LINE OF lt_ztint_wo_fl.
  DATA lt_ztint_user_inf TYPE STANDARD TABLE OF ztint_user_inf.
  DATA ls_ztint_user_inf LIKE LINE OF lt_ztint_user_inf.
  DATA lt_user_content1 TYPE  zsint_push_userlist_tab.
  DATA ls_user_content1 TYPE  zsint_push_userlist.
  DATA lt_deptlist TYPE STANDARD TABLE OF ztint_dept.
  DATA lt_dept_all TYPE STANDARD TABLE OF ztint_dept.
  DATA ls_dept LIKE LINE OF lt_dept_all.
  DATA lv_date1 TYPE char15.
  DATA lv_time1  TYPE char08.
  DATA lv_line TYPE i.
  DATA lt_url TYPE STANDARD TABLE OF string.
  DATA lv_msg_type1 TYPE string.
  DATA lv_title1 TYPE string.
  DATA lv_url1  TYPE string.
  DATA lv_content TYPE string.
  DATA lv_to_user  TYPE string.
  DATA lv_tims TYPE string.
  DATA l_tims TYPE string.
  DATA lt_formitemlist TYPE zsint_wordrecord_formitem_tab.
  DATA ls_formitemlist LIKE LINE OF lt_formitemlist.
  DATA ls_userlist_workrecord TYPE LINE OF zsint_push_userlist_tab.
  DATA lt_userlist_workrecord LIKE STANDARD TABLE OF ls_userlist_workrecord.
  DATA lt_recordlist TYPE  zsint_push_recordlist_tab.
  DATA lt_userno_tem TYPE STANDARD TABLE OF ty_userno.
  DATA ls_riskwo_log TYPE ztint_riskwo_log .
  DATA lt_riskwo_log TYPE STANDARD TABLE OF ztint_riskwo_log WITH DEFAULT KEY.
  DATA ls_next_hriskwo_f TYPE ztint_hriskwo_f.
  DATA lt_ztint_hriskwo_f TYPE STANDARD TABLE OF ztint_hriskwo_f WITH DEFAULT KEY.
  DATA(lo_hrisk) = NEW zcl_cassint_hriskwo( ).
  DATA(lo_asapi) = NEW zcl_icec_aftersale_api( ).
  DATA lv_log_msg1 TYPE string.
  DATA lv_fnam TYPE string.
  DATA lv_repairdesc TYPE string.
  DATA lv_value TYPE string.
  DATA ls_asinf TYPE zas_aftersaleinf.
  DATA lv_getted TYPE string. "工单空军自动领取标记
  DATA:lv_parts TYPE string.
  DATA:lv_amount TYPE p DECIMALS 2.

*** 1.APPEAL_CREATE 劣品申诉，退货转申诉 ***
*** FAST_COMPENSATION_CREATE 开思质保 AFTERSALE_CREATE 退货申请***
  lv_asid = es_afinf-asid.
  SELECT SINGLE * INTO @DATA(ls_data1) FROM ztint_wo_header WHERE asid = @es_afinf-asid AND wostatus EQ '1' AND isdelete = ''.
  IF sy-subrc EQ 0."在开思助手创建的申诉单更新领取状态同步至平台
    DATA(lv_oldwo) = 'X'.
    IF es_afinf-type EQ  'easyReturnAftersale' AND ls_data1-formicec EQ 'X'.
      EXIT.
    ENDIF.
    IF es_afinf-childtype EQ 'fastCompensationAftersale' OR es_afinf-childtype EQ  'easyReturnAftersale' OR es_afinf-childtype EQ  'fastClaim'.
      DATA(lv_handletime) = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
      lv_username = sy-uname.
      lv_statusid = COND #( WHEN es_afinf-righttype EQ 'fastCompensationAftersale'  THEN 'FAST_COMPENSATION_ACCEPT'
      WHEN es_afinf-righttype EQ 'easyReturnAftersale' THEN 'EASY_RETURN_ACCEPT' ).
      IF es_afinf-righttype EQ 'fastCompensationAftersale' OR  es_afinf-righttype EQ 'easyReturnAftersale' OR es_afinf-newtype EQ 'cassmallQA'.
        lv_repairdesc  = |开思已收到您的售后申请|.
        ls_asinf-aftersaleform-repairdesc  = |开思已收到您的售后申请|.
        ls_asinf-aftersaleform-platformdesc  = |开思已收到您的售后申请|.
      ENDIF.
      SELECT SINGLE value INTO @DATA(lv_usernewapi) FROM ztint_par WHERE fname = 'USERNEWAPI_UPDATEAS'.
      IF lv_usernewapi EQ 'X' OR  "新单走API同步信息
      ( es_afinf-type = 'cassmallQA' OR "新质保
      es_afinf-type = 'orderReturn' )."新退货
        ls_asinf-asid = es_afinf-asid.
        ls_asinf-handleusername = lv_username.
        ls_asinf-handletime = lv_handletime.
        ls_asinf-aftersaleform-asstatusid = lv_statusid.
        DATA(ls_result) = lo_asapi->update_aftersale_status( EXPORTING es_asinf = ls_asinf ).
        IF ls_result-errormessage IS NOT INITIAL.
          lv_orderno = ls_wo_header-woid.
          save_wo_log( EXPORTING event_id = 'GETWOSTATUSUPADTE' event_desc = '工单领取同步至平台' status = 'E'
            orderno = lv_orderno key_value1 = lv_asid message = ls_result-errormessage  ).
        ENDIF.
      ELSE.
        CALL FUNCTION 'Z_FMINT_SEND_MESSAGE_CUS'
          IN UPDATE TASK
          EXPORTING
            as_id        = lv_asid
            as_status_id = lv_statusid
            handle_time  = lv_handletime
            repairdesc   = lv_repairdesc
            iv_username  = lv_username.
      ENDIF.
    ENDIF.
    save_wo_log( EXPORTING event_id = 'INTAPPEALCERATE' event_desc = '平台申诉创建工单失败' status = 'E'
      key_value1 = lv_asid message = |售后申请ID已生成过工单'{ ls_data1-woid }| ).
    EXIT.
  ENDIF.
  "工单号
  DATA(lv_woid) = create_woid( asid = lv_asid ).
  CHECK lv_woid IS NOT INITIAL.

  SELECT SINGLE * INTO @DATA(ls_wotype) FROM ztint_wo_woitype
  WHERE newtype = @es_afinf-newtype  AND childtype EQ @es_afinf-childtype.
  ls_wo_header-woid = lv_woid.
  ls_wo_header-source = 'ICEC'.
  ls_wo_header-cusdescribe = es_afinf-comments."客户原始诉求
  ls_wo_header-wotype = 'R'.
  ls_wo_header-wochdtype = ls_wotype-wochdtype.
  ls_wo_header-woroottype = ls_wotype-woroottype.
  ls_wo_header-wostatus = '1'."流转中
  ls_wo_header-billtype = 'ORDER'.
  ls_wo_header-billno = es_afinf-originalsource.
  ls_wo_header-asid = es_afinf-asid.
  ls_wo_header-modelid = 'YY5'."通用
  ls_wo_header-company = es_afuser-asrepaircompanyid.
  ls_wo_header-companyname = es_afuser-asrepaircompanyname.
  ls_wo_header-formicec = 'X'."平台申诉
  ls_wo_header-processway = 'LZ'."是否流转
  ls_wo_header-usefultimes = usefultimes."权益剩余次数
  ls_wo_header-warrantyamount = warrantyamount."权益金额
  ls_wo_header-partsstateid = as_datail-as-usagestateid.
  ls_wo_header-partsstateiddesc = as_datail-as-accessoryinfo.
  ls_wo_header-currentplace = as_datail-as-currentplace."配件所在地
  ls_wo_header-isinstalled = as_datail-as-isinstalled."配件安装
  ls_wo_header-isintactdesc = as_datail-as-isintact."收货外包装
  ls_wo_header-reasondesc = as_datail-as-reasondesc."退货原因
  ls_wo_header-returntypedesc = as_datail-as-returntypedesc."退货类型
  ls_wo_header-isreceiveddesc = as_datail-as-isreceived."货物状态
  ls_wo_header-logisticsnum = as_datail-as-logisticstracknum."物流单号
  ls_wo_header-logisticscompany = as_datail-as-logisticscompany."物流公司
  IF es_afinf-childtype EQ 'returnAftersale'.
    ls_wo_header-appealreason =  COND #( WHEN as_datail-as-appealreason IS NOT INITIAL THEN as_datail-as-appealreason ELSE as_datail-as-comments ).
    ls_wo_header-cusdescribe = es_afinf-origincomments.
  ENDIF.
  "SELECT * INTO  TABLE @DATA(lt_afstatus) FROM zticec_af_status WHERE asid = @es_afinf-asid.
  "SORT lt_afstatus  BY id  ASCENDING .
  "READ TABLE lt_afstatus INTO DATA(ls_afstatustemp) INDEX 1.
  " ls_wo_header-cusdescribe = ls_afstatustemp-handlemessage.
  IF es_afinf-childtype EQ 'returnAftersale' AND es_afinf-type  NE 'returnAftersale'.
    READ TABLE as_datail-statusinfo INTO DATA(ls_status) WITH KEY asstatusid = 'AFTERSALE_CREATE'.
    IF ls_status-createdstamp IS NOT INITIAL.
      CLEAR:lv_date1,lv_time1.
      zcl_cassint_formatter=>convert_java_timestamp_to_abap(
      EXPORTING iv_timestamp = ls_status-createdstamp
      IMPORTING ev_date = lv_date1 ev_time = lv_time1 ).
      ls_wo_header-zcrt_bdate = lv_date1.
      ls_wo_header-zcrt_btime = lv_time1.
      ls_wo_header-zcrt_uname = ls_status-handleusername.
      ls_wo_header-zupd_bdate = lv_date1.
      ls_wo_header-zupd_btime = lv_time1.
      ls_wo_header-zupd_uname = ls_status-handleusername.
    ENDIF.
  ELSE.
    ls_wo_header-zcrt_bdate = es_afinf-createdate.
    ls_wo_header-zcrt_btime = es_afinf-createtime.
    ls_wo_header-zcrt_uname = es_afinf-createdname.
    ls_wo_header-zupd_bdate = es_afinf-createdate.
    ls_wo_header-zupd_btime = es_afinf-createtime.
    ls_wo_header-zupd_uname = es_afinf-zcrt_uname.
  ENDIF.
  ls_wo_header-appealsource = COND #( WHEN es_afinf-originalsourcetype EQ 'repair' THEN '1' "维修厂
  WHEN es_afinf-originalsourcetype EQ 'supplier' THEN '2' )."供应商
  "根据订单获取服务商
  lo_order_api->get_order_detail_data( EXPORTING iv_orderid = EXACT string( ls_wo_header-billno ) iv_showsource = 'PLATFORM'
  IMPORTING es_out = DATA(es_detail) ev_msg = DATA(ev_msg) ).
  ls_wo_header-productstoreid = es_afuser-assupplierstoreid.
  ls_wo_header-productstorename = es_afuser-assupplierstorename.
  ls_wo_header-vendor = es_detail-orderheader-fulfillstore-storeid.
  ls_wo_header-vendordesc = es_detail-orderheader-fulfillstore-storename.
  ls_wo_header-userloginid = es_afinf-createdby.
  ls_wo_header-userloginname = es_afinf-createdname.
  "SELECT SINGLE * INTO @DATA(ls_afstatus) FROM zticec_af_status WHERE asid = @es_afinf-asid
  " AND asstatusid IN ('FAST_COMPENSATION_CREATE','APPEAL_CREATE','EASY_RETURN_CREATE','AFTERSALE_CREATE').
  " ls_wo_header-cusdescribe = ls_afstatus-handlemessage."问题描述
  DATA(lo_obj) = NEW zcl_icec_user_api( ).
  lo_obj->get_cusaccount_bycompanyid( EXPORTING iv_companyid  = EXACT string( ls_wo_header-company )
  IMPORTING et_cusaccount = DATA(lt_cusaccount) ).
  SORT lt_cusaccount BY userloginid.
  READ TABLE lt_cusaccount INTO DATA(ls_cusaccount) WITH KEY userloginid = ls_wo_header-userloginid BINARY SEARCH.
  ls_wo_header-cellphone = ls_cusaccount-cellphone.
  "申诉工单新增vin码以及车辆等级
  SELECT SINGLE vin FROM zticec_inquiry_h INTO @ls_wo_header-vin WHERE inquiryid = @es_detail-orderheader-inquiryid.
  ls_wo_header-type = COND #( WHEN es_afinf-newtype EQ 'fakeAftersale'  OR es_afinf-childtype EQ 'returnAftersale'  OR es_afinf-newtype EQ 'orderCancel' OR es_afinf-newtype EQ 'sellerQA' THEN 'YY517'
  WHEN es_afinf-childtype EQ 'fastCompensationAftersale'  OR es_afinf-childtype EQ 'easyReturnAftersale' OR es_afinf-childtype EQ 'fastClaim'
  OR es_afinf-childtype EQ 'normalClaim' OR es_afinf-childtype EQ 'exemptReviewClaim' THEN 'YY518' )."
  ls_wo_header-typenewest = ls_wo_header-type.
  ls_wo_header-appealtype = COND #( WHEN es_afinf-newtype EQ 'fakeAftersale' THEN 'FAKE'
  WHEN es_afinf-childtype EQ 'returnAftersale' THEN 'RETURN'
  WHEN es_afinf-childtype EQ 'orderCancel' THEN 'CANCEL'
  WHEN es_afinf-childtype EQ 'fastClaim' THEN 'FASTCOM'
  WHEN es_afinf-childtype EQ 'fastCompensationAftersale' THEN 'FASTCOM'
  WHEN es_afinf-childtype EQ 'easyReturnAftersale' THEN 'EASYRE'
  WHEN es_afinf-newtype EQ 'sellerQA' THEN 'SELLERQA').
  ls_wo_header-righttype = COND #( WHEN es_afinf-newtype EQ 'cassmallQA' AND ( es_afinf-childtype EQ 'fastCompensationAftersale' OR  es_afinf-childtype EQ 'fastClaim' ) THEN 'FAST_COMPENSATION'
  WHEN es_afinf-childtype EQ 'easyReturnAftersale' THEN 'EASY_RETURN'
  WHEN es_afinf-childtype EQ 'fastCompensationAftersale' THEN 'FAST_COMPENSATION'
  WHEN es_afinf-childtype EQ 'fastClaim' THEN 'FAST_COMPENSATION'
  ELSE es_afinf-childtype ).
  IF ls_wo_header-righttype IS NOT INITIAL .
    SELECT SINGLE text INTO ls_wo_header-righttypedesc FROM ztbc_f4_config WHERE fnam = 'ZINT_CASSINT_RIGHTTYPE' AND val_high EQ ls_wo_header-righttype .
  ELSE.
    CLEAR ls_wo_header-righttypedesc.
  ENDIF.
*    ls_wo_header-righttypedesc = COND #( WHEN ls_wo_header-righttype EQ 'FAST_COMPENSATION'  THEN '快速理赔'
*                                         WHEN ls_wo_header-righttype EQ 'EASY_RETURN' THEN '无忧退货' ).
  ls_wo_header-isrightwo = COND #( WHEN ls_wo_header-righttype EQ 'FAST_COMPENSATION' OR ls_wo_header-righttype EQ 'EASY_RETURN' THEN 'X' ELSE '').

  SELECT SINGLE cargrade INTO ls_wo_header-custag FROM ztint_car_class WHERE carbrandid = ls_wo_header-carbrand.
  SELECT SINGLE * FROM ztint_cus_inf INTO @DATA(ls_ztint_cus_inf) WHERE companyid = @ls_wo_header-company .
  ls_wo_header-zoneid = ls_ztint_cus_inf-zoneid."战区
  "车型品牌
  SELECT SINGLE * FROM zticec_order_h INTO @DATA(ls_zticec_order_h) WHERE orderid = @es_afinf-originalsource.
  ls_wo_header-carbrand = ls_zticec_order_h-carbrandid.
  ls_wo_header-carbrandname = ls_zticec_order_h-carbrandname.

  "工单item
  LOOP AT et_aforder INTO DATA(ls_aforder).
    ls_wo_item-woid = ls_wo_header-woid.
    ls_wo_item-posnr = sy-tabix.
    ls_wo_item-billno = ls_aforder-orderid.
    ls_wo_item-orderid = ls_aforder-orderid.
    ls_wo_item-orderitemseqid = ls_aforder-orderitemseqid.
    ls_wo_item-partsnum = ls_aforder-productnum.
    ls_wo_item-partsname = ls_aforder-productname.
    ls_wo_item-brandid = ls_aforder-productbrandid.
    ls_wo_item-brandname = ls_aforder-productbrandname.
    ls_wo_item-acturalprice = ls_aforder-actualprice.
    ls_wo_item-quality = ls_aforder-quality.
    ls_wo_item-qualityname = ls_aforder-qualityname.
    ls_wo_item-selquantity = ls_aforder-quantity.
    ls_wo_item-zcrt_bdate = ls_wo_header-zcrt_bdate."es_afinf-createdate.
    ls_wo_item-zcrt_btime = ls_wo_header-zcrt_btime."es_afinf-createtime.
    ls_wo_item-zcrt_uname = ls_wo_header-zcrt_uname."es_afinf-createdname.
    ls_wo_item-zupd_bdate = ls_wo_header-zcrt_bdate.
    ls_wo_item-zupd_btime = ls_wo_header-zcrt_btime.
    ls_wo_item-zupd_uname = ls_wo_header-zcrt_uname.
    lv_parts = COND #( WHEN lv_parts IS INITIAL THEN |{ ls_wo_item-partsname }({ ls_wo_item-brandname  }),{ ls_wo_item-acturalprice }元,{ ls_wo_item-qualityname }|
                            ELSE |{ lv_parts } \\ n { ls_wo_item-partsname }({ ls_wo_item-brandname  }),{ ls_wo_item-acturalprice }元,{ ls_wo_item-qualityname }| ).
    lv_amount = lv_amount + ls_wo_item-selquantity * ls_wo_item-acturalprice.
    APPEND ls_wo_item TO lt_wo_item.
    CLEAR: ls_aforder,ls_wo_item.
  ENDLOOP.
  "创建节点
  TRY .
      DATA(lv_noteid) = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
    CATCH cx_uuid_error.
  ENDTRY.
  ls_ztint_wo_flow-woid = ls_wo_header-woid.
  ls_ztint_wo_flow-flowno = '1'.
  ls_ztint_wo_flow-noteid = lv_noteid.
  ls_ztint_wo_flow-notestatus = '6'."已创建
  ls_ztint_wo_flow-node = 'N0000'."事工单创建
  ls_ztint_wo_flow-zcrt_bdate = ls_wo_header-zcrt_bdate."es_afinf-createdate.
  ls_ztint_wo_flow-zcrt_btime = ls_wo_header-zcrt_btime."es_afinf-createtime.
  ls_ztint_wo_flow-zcrt_uname = ls_wo_header-zcrt_uname."es_afinf-createdname.
  ls_ztint_wo_flow-zupd_bdate = ls_wo_header-zcrt_bdate."es_afinf-createdate.
  ls_ztint_wo_flow-zupd_btime = ls_wo_header-zcrt_btime."es_afinf-createtime.
  ls_ztint_wo_flow-zupd_uname = ls_wo_header-zcrt_uname."es_afinf-createdname.
  APPEND ls_ztint_wo_flow TO lt_ztint_wo_flow.

  CLEAR ls_ztint_wo_note.
  ls_ztint_wo_note-woid = ls_wo_header-woid.
  ls_ztint_wo_note-noteid = lv_noteid.
  ls_ztint_wo_note-notestatus = '6'."已创建
  ls_ztint_wo_note-type = ls_wo_header-type.
  ls_ztint_wo_note-zcrt_bdate = ls_wo_header-zcrt_bdate.
  ls_ztint_wo_note-zcrt_btime = ls_wo_header-zcrt_btime.
  ls_ztint_wo_note-zcrt_uname = ls_wo_header-zcrt_uname.
  ls_ztint_wo_note-zupd_bdate = ls_wo_header-zcrt_bdate.
  ls_ztint_wo_note-zupd_btime = ls_wo_header-zcrt_btime.
  ls_ztint_wo_note-zupd_uname = ls_wo_header-zcrt_uname.
  APPEND ls_ztint_wo_note TO lt_ztint_wo_note.

  "nouser.
  CLEAR ls_ztint_wo_nouser.
  ls_ztint_wo_nouser-woid = ls_wo_header-woid.
  ls_ztint_wo_nouser-noteid = lv_noteid.
  ls_ztint_wo_nouser-userid = ''.
  ls_ztint_wo_nouser-usertype = '6'."创建人
  ls_ztint_wo_nouser-zcrt_bdate = ls_wo_header-zcrt_bdate.
  ls_ztint_wo_nouser-zcrt_btime = ls_wo_header-zcrt_btime.
  ls_ztint_wo_nouser-zcrt_uname = ls_wo_header-zcrt_uname.
  ls_ztint_wo_nouser-zupd_bdate = ls_wo_header-zcrt_bdate.
  ls_ztint_wo_nouser-zupd_btime = ls_wo_header-zcrt_btime.
  ls_ztint_wo_nouser-zupd_uname = ls_wo_header-zcrt_uname.
  APPEND ls_ztint_wo_nouser TO lt_ztint_wo_nouser.


  "待领取人"取客户配置表
  DATA:lr_userid TYPE RANGE OF ztint_user_inf-userid.
******  DATA:lt_msglist    TYPE STANDARD TABLE OF ztint_msg_list.

******  SELECT SINGLE holiday INTO @DATA(lv_holiday) FROM ztint_calendar WHERE zdate = @sy-datum.

******  "20240530--有空军直接指定到空军处理
******  SELECT SINGLE companycode,cusname,u~userid,ui~username INTO @DATA(ls_air)
******  FROM ztint_cus_inf AS i
******  INNER JOIN ztint_cus_user AS u ON i~cusid = u~cusid AND u~usertype = '2' AND u~userchildtype = 'A' AND u~isdelete = ''
******  INNER JOIN ztint_user_inf AS ui ON u~userid = ui~userid AND ui~isstill = 'X'
******  WHERE companyid = @ls_wo_header-company.
******  IF sy-subrc EQ 0 AND lv_holiday IS INITIAL.
******    "自动领取---空军
******    lv_getted = 'X'.  "空军自动领取工单标记
******    CLEAR ls_ztint_wo_flow.
******    TRY .
******        DATA(lv_noteid_next) = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
******      CATCH cx_uuid_error.
******    ENDTRY.
******    ls_ztint_wo_flow-woid = ls_wo_header-woid.
******    ls_ztint_wo_flow-flowno = '2'.
******    ls_ztint_wo_flow-noteid = lv_noteid_next.
******    ls_ztint_wo_flow-notestatus = '3'."自动领取
******    ls_ztint_wo_flow-node = 'N0006'."事件：订单确认
******    ls_ztint_wo_flow-zcrt_bdate = ls_wo_header-zcrt_bdate.
******    ls_ztint_wo_flow-zcrt_btime = ls_wo_header-zcrt_btime.
******    ls_ztint_wo_flow-zcrt_uname = ls_air-username.
******    ls_ztint_wo_flow-zupd_bdate = ls_wo_header-zcrt_bdate.
******    ls_ztint_wo_flow-zupd_btime = ls_wo_header-zcrt_btime.
******    ls_ztint_wo_flow-zupd_uname = ls_air-username.
******    APPEND ls_ztint_wo_flow TO lt_ztint_wo_flow.
******
******    CLEAR ls_ztint_wo_note.
******    ls_ztint_wo_note-woid = ls_wo_header-woid.
******    ls_ztint_wo_note-noteid = lv_noteid_next.
******    ls_ztint_wo_note-notestatus = '3'."已领取领取
******    ls_ztint_wo_note-zcrt_bdate = ls_wo_header-zcrt_bdate.
******    ls_ztint_wo_note-zcrt_btime = ls_wo_header-zcrt_btime.
******    ls_ztint_wo_note-zcrt_uname = ls_air-username.
******    ls_ztint_wo_note-zupd_bdate = ls_wo_header-zcrt_bdate.
******    ls_ztint_wo_note-zupd_btime = ls_wo_header-zcrt_btime.
******    ls_ztint_wo_note-zupd_uname = ls_air-username.
******    APPEND ls_ztint_wo_note TO lt_ztint_wo_note.
******
******    CLEAR ls_ztint_wo_nouser.
******    ls_ztint_wo_nouser-woid = ls_wo_header-woid.
******    ls_ztint_wo_nouser-noteid = lv_noteid_next.
******    ls_ztint_wo_nouser-userid = ls_air-userid.
******    ls_ztint_wo_nouser-usertype = '2'."处理人
******    ls_ztint_wo_nouser-zcrt_bdate = ls_wo_header-zcrt_bdate.
******    ls_ztint_wo_nouser-zcrt_btime = ls_wo_header-zcrt_btime.
******    ls_ztint_wo_nouser-zcrt_uname = ls_air-username.
******    ls_ztint_wo_nouser-zupd_bdate = ls_wo_header-zcrt_bdate.
******    ls_ztint_wo_nouser-zupd_btime = ls_wo_header-zcrt_btime.
******    ls_ztint_wo_nouser-zupd_uname = ls_air-username.
******    APPEND ls_ztint_wo_nouser TO lt_ztint_wo_nouser.
******
******    "空军跟进节点
******    CLEAR:lv_noteid_next,ls_ztint_wo_flow.
******    TRY .
******        lv_noteid_next = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
******      CATCH cx_uuid_error.
******    ENDTRY.
******    ls_ztint_wo_flow-woid = ls_wo_header-woid.
******    ls_ztint_wo_flow-flowno = '3'.
******    ls_ztint_wo_flow-noteid = lv_noteid_next.
******    ls_ztint_wo_flow-notestatus = '2'."
******    ls_ztint_wo_flow-node = 'N0019'."事件：订单确认
******    ls_ztint_wo_flow-zcrt_bdate = ls_wo_header-zcrt_bdate.
******    ls_ztint_wo_flow-zcrt_btime = ls_wo_header-zcrt_btime.
******    ls_ztint_wo_flow-zcrt_uname = ls_air-username.
******    ls_ztint_wo_flow-zupd_bdate = ls_wo_header-zcrt_bdate.
******    ls_ztint_wo_flow-zupd_btime = ls_wo_header-zcrt_btime.
******    ls_ztint_wo_flow-zupd_uname = ls_air-username.
******    APPEND ls_ztint_wo_flow TO lt_ztint_wo_flow.
******
******    CLEAR ls_ztint_wo_note.
******    ls_ztint_wo_note-woid = ls_wo_header-woid.
******    ls_ztint_wo_note-noteid = lv_noteid_next.
******    ls_ztint_wo_note-notestatus = '2'."待处理
******    ls_ztint_wo_note-zcrt_bdate = ls_wo_header-zcrt_bdate.
******    ls_ztint_wo_note-zcrt_btime = ls_wo_header-zcrt_btime.
******    ls_ztint_wo_note-zcrt_uname = ls_air-username.
******    ls_ztint_wo_note-zupd_bdate = ls_wo_header-zcrt_bdate.
******    ls_ztint_wo_note-zupd_btime = ls_wo_header-zcrt_btime.
******    ls_ztint_wo_note-zupd_uname = ls_air-username.
******    APPEND ls_ztint_wo_note TO lt_ztint_wo_note.
******
******
******    CLEAR: ls_ztint_wo_nouser,lt_formitemlist.
******    ls_ztint_wo_nouser-woid = ls_wo_header-woid.
******    ls_ztint_wo_nouser-noteid = lv_noteid_next.
******    ls_ztint_wo_nouser-userid = ls_air-userid.
******    ls_ztint_wo_nouser-usertype = '2'."待处理人呢
******    ls_ztint_wo_nouser-zcrt_bdate = sy-datum.
******    ls_ztint_wo_nouser-zcrt_btime = sy-uzeit.
******    ls_ztint_wo_nouser-zupd_bdate = sy-datum.
******    ls_ztint_wo_nouser-zupd_btime = sy-uzeit.
******    APPEND ls_ztint_wo_nouser TO lt_ztint_wo_nouser.
******
*******增加待办推送
******    lr_userid = VALUE #( BASE lr_userid ( sign = 'I' option = 'EQ' low = ls_air-userid ) ).
******    ls_userlist_workrecord-userid = ls_air-userid.
******    APPEND ls_userlist_workrecord TO lt_userlist_workrecord.
******    ls_formitemlist-title = '待办事项'.
******    ls_formitemlist-content = '工单号: ' && ls_wo_header-woid && '需要您处理，请关注！'.
******    APPEND ls_formitemlist TO lt_formitemlist.
******    CLEAR:ls_formitemlist.
******
******    CONCATENATE  lv_date1 lv_time1  INTO l_tims SEPARATED BY ' '.
******    ls_formitemlist-title = '时间'.
******    ls_formitemlist-content = l_tims .
******    APPEND ls_formitemlist TO lt_formitemlist.
******    CLEAR:ls_formitemlist,ls_ztint_user_inf,ls_userlist_workrecord, ls_formitemlist.
******
******    "工单创建推送给空军
******    TRY.
******        DATA(lv_messageid) = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
******        lt_msglist = VALUE #( BASE lt_msglist ( messageid = lv_messageid
******        msgfirstcat = 'AS' msgsecondcat = 'NEWWO'   "大单询价单
******        touserid = ls_air-userid senddate = sy-datum sendtime = sy-uzeit msgstatus = '1'
******        msgkeyvalue = ls_wo_header-woid msgkeyvalue_e = |{ ls_air-companycode }|
******        msgtitle = |客户{ ls_air-cusname }({ ls_air-companycode })有工单{ ls_wo_header-woid }生成，请及时跟进处理！|
******        msgcontent = |客户{ ls_air-cusname }({ ls_air-companycode })有工单{ ls_wo_header-woid }生成，请及时跟进处理！|
******        msgurl = ||
******        zcrt_bdate = sy-datum zcrt_btime = sy-uzeit zupd_bdate = sy-datum zupd_btime = sy-uzeit ) ).
******      CATCH cx_uuid_error INTO DATA(lcx_nofound).
******    ENDTRY.


**  CLEAR:lv_noteid_next,ls_ztint_wo_flow.
  TRY .
      DATA(lv_noteid_next) = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
    CATCH cx_uuid_error.
  ENDTRY.
  ls_ztint_wo_flow-woid = ls_wo_header-woid.
  ls_ztint_wo_flow-flowno = '2'.
  ls_ztint_wo_flow-noteid = lv_noteid_next.
  ls_ztint_wo_flow-notestatus = '1'."待领取
  ls_ztint_wo_flow-node = 'N0006'."事件：订单确认
  ls_ztint_wo_flow-zcrt_bdate = ls_wo_header-zcrt_bdate.
  ls_ztint_wo_flow-zcrt_btime = ls_wo_header-zcrt_btime.
  ls_ztint_wo_flow-zcrt_uname = ls_wo_header-zcrt_uname.
  ls_ztint_wo_flow-zupd_bdate = ls_wo_header-zcrt_bdate.
  ls_ztint_wo_flow-zupd_btime = ls_wo_header-zcrt_btime.
  ls_ztint_wo_flow-zupd_uname = ls_wo_header-zcrt_uname.
  APPEND ls_ztint_wo_flow TO lt_ztint_wo_flow.

  CLEAR ls_ztint_wo_note.
  ls_ztint_wo_note-woid = ls_wo_header-woid.
  ls_ztint_wo_note-noteid = lv_noteid_next.
  ls_ztint_wo_note-notestatus = '1'."待领取
  ls_ztint_wo_note-zcrt_bdate = ls_wo_header-zcrt_bdate.
  ls_ztint_wo_note-zcrt_btime = ls_wo_header-zcrt_btime.
  ls_ztint_wo_note-zcrt_uname = ls_wo_header-zcrt_uname.
  ls_ztint_wo_note-zupd_bdate = ls_wo_header-zcrt_bdate.
  ls_ztint_wo_note-zupd_btime = ls_wo_header-zcrt_btime.
  ls_ztint_wo_note-zupd_uname = ls_wo_header-zcrt_uname.
  APPEND ls_ztint_wo_note TO lt_ztint_wo_note.

  SELECT SINGLE ksbsorder INTO @DATA(lv_ksbsorder) FROM zticec_order_h WHERE orderid = @ls_wo_header-billno.
  SELECT SINGLE owner INTO @DATA(lv_ownerrole) FROM ztint_wo_woitype
   WHERE wotype = @ls_wo_header-wotype AND woroottype = @ls_wo_header-woroottype.
  "当前售后处理的单据：非严选+劣品申诉/快速理赔/普通质保/商家质保申诉
*  IF lv_ksbsorder IS INITIAL AND ( ls_wo_header-woroottype = 'Q2' OR ls_wo_header-woroottype = 'Q3' )."普通质保/快速理赔--开思质保 --推数给指定的售后人员
  IF lv_ksbsorder IS INITIAL AND lv_ownerrole EQ 'S'."售后角色处理
    "获取收货地址是最基础的
    SELECT SINGLE provincegeoid,citygeoid,countygeoid,villagegeoid FROM zticec_order_pa
      INTO @DATA(ls_addr) WHERE orderid = @ls_wo_header-billno.
    IF sy-subrc EQ 0.
      DATA(lv_shown_flg) = 'X'.
      "获取@负责人
      SELECT SINGLE useridstr,usernamestr,lastuseridstr,lastusernamestr,robotdeptid,robotdeptnm,amount INTO @DATA(ls_noticeuser)
        FROM ztint_wo_shown
        WHERE provincegeoid = @ls_addr-provincegeoid
          AND citygeoid = @ls_addr-citygeoid
          AND countygeoid = @ls_addr-countygeoid.
      IF sy-subrc NE 0.
        "获取@负责人
        SELECT SINGLE useridstr,usernamestr,lastuseridstr,lastusernamestr,robotdeptid,robotdeptnm,amount
          INTO CORRESPONDING FIELDS OF @ls_noticeuser
          FROM ztint_wo_shown
          WHERE provincegeoid = @ls_addr-provincegeoid
            AND citygeoid = @ls_addr-citygeoid.
      ENDIF.
      IF ls_noticeuser IS NOT INITIAL.
        "北京工单群@@负责人按金额有区分
        IF lv_amount <= ls_noticeuser-amount.
          SELECT c~userid,i~username INTO TABLE @DATA(lt_sp_noticeuser)
            FROM ztint_appeal_csc AS c
           INNER JOIN ztint_user_inf AS i ON c~userid = i~userid AND i~isstill = 'X'
         WHERE zoneid = 'SHSPLEADER' AND deptid = @ls_noticeuser-robotdeptid.
          IF sy-subrc EQ 0 .
            CLEAR:ls_noticeuser-useridstr,ls_noticeuser-usernamestr.
            ls_noticeuser-useridstr = REDUCE #( INIT x TYPE string FOR spuser IN lt_sp_noticeuser
                                               NEXT x = COND #( WHEN x IS INITIAL THEN spuser-userid
                                                                 ELSE |{ x },{ spuser-userid }| ) ).
            ls_noticeuser-usernamestr = REDUCE #( INIT y TYPE string FOR spuser IN lt_sp_noticeuser
                                               NEXT y = COND #( WHEN y IS INITIAL THEN spuser-username
                                                                 ELSE |{ y },{ spuser-username }| ) ).
          ENDIF.
        ENDIF.
        "获取推送消息的群
        SELECT SINGLE robotkey FROM ztint_dept_robot INTO @DATA(lv_robotkey)
          WHERE busitype = 'SHWO' AND deptid = @ls_noticeuser-robotdeptid.
        "获取对应配置的可领取人
        SELECT c~userid INTO CORRESPONDING FIELDS OF TABLE lt_ztint_user_inf
          FROM ztint_appeal_csc AS c
          INNER JOIN ztint_user_inf AS i ON c~userid = i~userid AND i~isstill = 'X'
          WHERE zoneid = 'SHOWNER' AND deptid = ls_noticeuser-robotdeptid.
        IF sy-subrc NE 0."兜底人处理
          SELECT c~userid INTO CORRESPONDING FIELDS OF TABLE lt_ztint_user_inf
            FROM ztint_appeal_csc AS c
            INNER JOIN ztint_user_inf AS i ON c~userid = i~userid AND i~isstill = 'X'
            WHERE zoneid = 'SHLASTOWNER' AND deptid = ls_noticeuser-robotdeptid.
        ENDIF.
      ENDIF.
      IF lt_ztint_user_inf IS INITIAL.
        CLEAR:lv_shown_flg."没有相应的领取人，最后还是客服兜底
      ELSE.
        ls_wo_header-shneedprocess = 'X'."售后待领取标识
      ENDIF.
    ENDIF.
  ENDIF.

  IF lv_shown_flg IS INITIAL."客服的处理人
    IF ls_wo_header-zoneid IS NOT INITIAL.
      SELECT * FROM ztint_appeal_csc INTO TABLE @DATA(lt_ztint_appeal_csc)
      WHERE zoneid = @ls_wo_header-zoneid.
    ENDIF.
    "如果没取到就取所有人
    IF lt_ztint_appeal_csc IS  INITIAL.
      SELECT * FROM ztint_appeal_csc INTO CORRESPONDING FIELDS OF TABLE @lt_ztint_appeal_csc
      WHERE zoneid = 'all'.
    ENDIF.
    "所有子部门
    LOOP AT  lt_ztint_appeal_csc INTO DATA(ls_ztint_appeal_csc) WHERE deptid IS NOT INITIAL ..
      ls_dept-deptid = ls_ztint_appeal_csc-deptid.
      APPEND ls_dept TO lt_dept_all.
      CLEAR lt_deptlist.
      CALL FUNCTION 'Z_FMINT_GET_SON_DEPT'
        EXPORTING
          iv_deptid   = ls_ztint_appeal_csc-deptid
        TABLES
          ot_deptlist = lt_deptlist.
      IF lt_deptlist IS NOT INITIAL.
        APPEND LINES OF lt_deptlist TO lt_dept_all.
      ENDIF.
      CLEAR:ls_ztint_appeal_csc,ls_dept..
    ENDLOOP.
    "取人员
    IF lt_dept_all IS NOT INITIAL.
      SELECT  c~* INTO CORRESPONDING FIELDS OF TABLE @lt_ztint_user_inf FROM ztint_dept AS a
      INNER JOIN ztint_user_dept AS b  ON b~deptid = a~deptid
      INNER JOIN ztint_user_inf AS c ON c~userid = b~userid
      FOR ALL ENTRIES IN @lt_dept_all
      WHERE a~deptid = @lt_dept_all-deptid
      AND b~isstill = 'X'
      AND c~isstill = 'X'.
    ENDIF.

    LOOP AT  lt_ztint_appeal_csc INTO ls_ztint_appeal_csc WHERE userid IS NOT INITIAL .
      CLEAR ls_ztint_user_inf.
      ls_ztint_user_inf-userid = ls_ztint_appeal_csc-userid.
      APPEND ls_ztint_user_inf TO lt_ztint_user_inf.
    ENDLOOP.
    SORT lt_ztint_user_inf BY userid.
    DELETE ADJACENT DUPLICATES FROM lt_ztint_user_inf COMPARING userid."人员排序
  ENDIF.
  "第二个节点待处理人
  LOOP AT  lt_ztint_user_inf INTO ls_ztint_user_inf.
    CLEAR: ls_ztint_wo_nouser  ,lt_formitemlist.
    ls_ztint_wo_nouser-woid = ls_wo_header-woid.
    ls_ztint_wo_nouser-noteid = lv_noteid_next.
    ls_ztint_wo_nouser-userid = ls_ztint_user_inf-userid.
    ls_ztint_wo_nouser-usertype = '1'."待处理人呢
    ls_ztint_wo_nouser-zcrt_bdate = sy-datum.
    ls_ztint_wo_nouser-zcrt_btime = sy-uzeit.
    ls_ztint_wo_nouser-zupd_bdate = sy-datum.
    ls_ztint_wo_nouser-zupd_btime = sy-uzeit.
    APPEND ls_ztint_wo_nouser TO lt_ztint_wo_nouser.

*增加待办推送
    lr_userid = VALUE #( BASE lr_userid ( sign = 'I' option = 'EQ' low = ls_ztint_user_inf-userid ) ).
    ls_userlist_workrecord-userid = ls_ztint_user_inf-userid.
    APPEND ls_userlist_workrecord TO lt_userlist_workrecord.
    ls_formitemlist-title = '待办事项'.
    ls_formitemlist-content = '工单号: ' && ls_wo_header-woid && '需要您处理，请关注！'.
    APPEND ls_formitemlist TO lt_formitemlist.
    CLEAR:ls_formitemlist.

    CONCATENATE  lv_date1 lv_time1  INTO l_tims SEPARATED BY ' '.
    ls_formitemlist-title = '时间'.
    ls_formitemlist-content = l_tims .
    APPEND ls_formitemlist TO lt_formitemlist.
    CLEAR:ls_formitemlist,ls_ztint_user_inf,ls_userlist_workrecord, ls_formitemlist.
  ENDLOOP.

  "抄送人
  "客户经理
  SELECT SINGLE * FROM ztint_cus_inf INTO @ls_ztint_cus_inf WHERE companyid = @ls_wo_header-company.
  IF ls_ztint_cus_inf-cusid IS NOT INITIAL.
    SELECT * FROM ztint_cus_user INTO TABLE @DATA(lt_ztint_cus_user)
    WHERE cusid = @ls_ztint_cus_inf-cusid AND isdelete = ''.
    IF lt_ztint_cus_user IS NOT INITIAL.
      LOOP AT  lt_ztint_cus_user INTO DATA(ls_ztint_cus_user).
        CLEAR ls_ztint_wo_nouser.
        ls_ztint_wo_nouser-woid = ls_wo_header-woid.
        ls_ztint_wo_nouser-noteid = lv_noteid.
        ls_ztint_wo_nouser-userid = ls_ztint_cus_user-userid.
        ls_ztint_wo_nouser-usertype = '3'."抄送人
        ls_ztint_wo_nouser-zcrt_bdate = ls_wo_header-zcrt_bdate.
        ls_ztint_wo_nouser-zcrt_btime = ls_wo_header-zcrt_btime.
        ls_ztint_wo_nouser-zcrt_uname = ls_wo_header-zcrt_uname.
        ls_ztint_wo_nouser-zupd_bdate = ls_wo_header-zcrt_bdate.
        ls_ztint_wo_nouser-zupd_btime = ls_wo_header-zcrt_btime.
        ls_ztint_wo_nouser-zupd_uname = ls_wo_header-zcrt_uname.
        APPEND ls_ztint_wo_nouser TO lt_ztint_wo_nouser.

        CLEAR ls_user_content.
        ls_user_content-userid = ls_ztint_cus_user-userid.
        ls_user_content-content = ' <font COLOR=#FF8C00>开思助手</font> \n' && '##### **平台申诉工单号: ' && ls_wo_header-woid && '有抄送给您，请关注！** \n'.
        lv_date1 = |{ sy-datum+0(4) }年{ sy-datum+4(2) }月{ sy-datum+6(2) }日|.
        lv_time1  = |{ sy-uzeit TIME = ISO }|.
        CONCATENATE '#####' lv_date1 lv_time1  INTO lv_tims SEPARATED BY ' '.
        "ls_user_content-content = ls_user_content-content &&  ' \n' && '<font COLOR=#DC143C>重大</font>' && '\n' && lv_tims.
        ls_user_content-orderno = ls_wo_header-woid.
        ls_user_content-firstcatgory = '工单中心'.
        ls_user_content-secondcatgory = '工单抄送'.
        CONCATENATE lv_date1 lv_time1  INTO lv_tims SEPARATED BY ' '.
        ls_user_content-wxcontent = |<div class=\\"gray\\">{ lv_date1 } { lv_time1  }</div>  <div class=\\"normal\\">平台申诉工单号:{ ls_wo_header-woid }有抄送给您，请关注！</div>|.
        APPEND ls_user_content1 TO lt_user_content1.
        CLEAR ls_ztint_cus_user.
      ENDLOOP.
    ENDIF.
    "add 客户经理的班长连长
    DATA(lv_yearmonth) = sy-datum+0(6).
    SELECT a~yearmonth,a~userno,a~teamid INTO TABLE @DATA(lt_team) FROM ztint_user_group AS a
    INNER JOIN ztint_user_inf AS b ON a~userno = b~userno
    INNER JOIN ztint_cus_user AS c ON b~userid = c~userid
    WHERE c~cusid = @ls_ztint_cus_inf-cusid
    AND c~usertype = '1'
    AND c~isdelete = ''
    AND a~yearmonth = @lv_yearmonth.
    IF lt_team IS NOT INITIAL.
      SELECT squadno,companyno INTO TABLE @DATA(lt_userno) FROM ztint_team_targe
      FOR ALL ENTRIES IN @lt_team WHERE yearmonth = @lt_team-yearmonth AND teamid = @lt_team-teamid.
      IF lt_userno IS NOT INITIAL.
        SELECT userid,username INTO TABLE @DATA(lt_userid) FROM ztint_user_inf AS a
        FOR ALL ENTRIES IN @lt_userno WHERE ( userno = @lt_userno-squadno OR userno = @lt_userno-companyno )
        AND a~isstill = 'X' AND userno NE ''. "modify.20210907-员工号不能为空
        LOOP AT lt_userno INTO DATA(ls_userno) WHERE squadno CA ','."一个团队多个班长
          SPLIT ls_userno-squadno AT ',' INTO TABLE lt_userno_tem.
          SELECT userid,username APPENDING CORRESPONDING FIELDS OF TABLE @lt_userid FROM ztint_user_inf
          FOR ALL ENTRIES IN @lt_userno_tem WHERE userno = @lt_userno_tem-userno AND isstill EQ 'X' AND userno NE ''.
          CLEAR:ls_userno,lt_userno_tem[].
        ENDLOOP.
        IF lt_userid IS NOT INITIAL.
          SORT lt_userid BY userid.
          DELETE ADJACENT DUPLICATES FROM lt_userid COMPARING userid.

          LOOP AT lt_userid INTO DATA(ls_userid).
            CLEAR ls_ztint_wo_nouser.
            ls_ztint_wo_nouser-woid = ls_wo_header-woid.
            ls_ztint_wo_nouser-noteid = lv_noteid.
            ls_ztint_wo_nouser-userid = ls_userid-userid.
            ls_ztint_wo_nouser-usertype = '3'."抄送人
            ls_ztint_wo_nouser-zcrt_bdate = ls_wo_header-zcrt_bdate.
            ls_ztint_wo_nouser-zcrt_btime = ls_wo_header-zcrt_btime.
            ls_ztint_wo_nouser-zcrt_uname = ls_wo_header-zcrt_uname.
            ls_ztint_wo_nouser-zupd_bdate = ls_wo_header-zcrt_bdate.
            ls_ztint_wo_nouser-zupd_btime = ls_wo_header-zcrt_btime.
            ls_ztint_wo_nouser-zupd_uname = ls_wo_header-zcrt_uname.
            APPEND ls_ztint_wo_nouser TO lt_ztint_wo_nouser.
            CLEAR ls_user_content.
            ls_user_content-userid = ls_userid-userid.
            ls_user_content-content = ' <font COLOR=#FF8C00>开思助手</font> \n' && '##### **平台申诉工单号: ' && ls_wo_header-woid && '有抄送给您，请关注！** \n'.
            lv_date1 = |{ sy-datum+0(4) }年{ sy-datum+4(2) }月{ sy-datum+6(2) }日|.
            lv_time1  = |{ sy-uzeit TIME = ISO }|.
            CONCATENATE '#####' lv_date1 lv_time1  INTO lv_tims SEPARATED BY ' '.
            ls_user_content-content = ls_user_content-content  && '\n' && lv_tims.
            ls_user_content-orderno = ls_wo_header-woid.
            ls_user_content-firstcatgory = '工单中心'.
            ls_user_content-secondcatgory = '工单抄送'.
            CONCATENATE lv_date1 lv_time1  INTO lv_tims SEPARATED BY ' '.
            ls_user_content-wxcontent = |<div class=\\"gray\\">{ lv_date1 } { lv_time1  }</div>  <div class=\\"normal\\">平台申诉工单号:{ ls_wo_header-woid }有抄送给您，请关注！</div>|.
            APPEND ls_user_content1 TO lt_user_content1.
            CLEAR ls_userid.
          ENDLOOP.
        ENDIF.
      ENDIF.
    ENDIF.
  ENDIF.
  "供应商拓展
  SELECT SINGLE * FROM ztint_ven_inf
  INTO @DATA(ls_ztint_ven_inf)
  WHERE productstoreid = @ls_wo_header-vendor.
  IF ls_ztint_ven_inf-venid IS NOT INITIAL.
    SELECT * FROM ztint_ven_user INTO TABLE @DATA(lt_ztint_ven_user)
    WHERE venid = @ls_ztint_ven_inf-venid
    AND isdelete = ''.
    IF lt_ztint_ven_user IS NOT INITIAL.
      LOOP AT  lt_ztint_ven_user INTO DATA(ls_ztint_ven_user).
        CLEAR ls_ztint_wo_nouser.
        ls_ztint_wo_nouser-woid = ls_wo_header-woid.
        ls_ztint_wo_nouser-noteid = lv_noteid.
        ls_ztint_wo_nouser-userid = ls_ztint_ven_user-userid.
        ls_ztint_wo_nouser-usertype = '3'."抄送人
        ls_ztint_wo_nouser-zcrt_bdate = ls_wo_header-zcrt_bdate.
        ls_ztint_wo_nouser-zcrt_btime = ls_wo_header-zcrt_btime.
        ls_ztint_wo_nouser-zcrt_uname = ls_wo_header-zcrt_uname.
        ls_ztint_wo_nouser-zupd_bdate = ls_wo_header-zcrt_bdate.
        ls_ztint_wo_nouser-zupd_btime = ls_wo_header-zcrt_btime.
        ls_ztint_wo_nouser-zupd_uname = ls_wo_header-zcrt_uname.
        APPEND ls_ztint_wo_nouser TO lt_ztint_wo_nouser.

        CLEAR ls_user_content.
        ls_user_content-userid = ls_ztint_ven_user-userid.
        ls_user_content-content = ' <font COLOR=#FF8C00>开思助手</font> \n' && '##### **平台申诉工单号: ' && ls_wo_header-woid && '有抄送给您，请关注！** \n'.
        lv_date1 = |{ sy-datum+0(4) }年{ sy-datum+4(2) }月{ sy-datum+6(2) }日|.
        lv_time1  = |{ sy-uzeit TIME = ISO }|.
        CONCATENATE '#####' lv_date1 lv_time1  INTO lv_tims SEPARATED BY ' '.
        ls_user_content-content = ls_user_content-content &&  ' \n'  && lv_tims.
        ls_user_content-orderno = ls_wo_header-woid.
        ls_user_content-firstcatgory = '工单中心'.
        ls_user_content-secondcatgory = '工单抄送'.
        CONCATENATE lv_date1 lv_time1  INTO lv_tims SEPARATED BY ' '.
        ls_user_content-wxcontent = |<div class=\\"gray\\">{ lv_date1 } { lv_time1  }</div>  <div class=\\"normal\\">平台申诉工单号:{ ls_wo_header-woid }有抄送给您，请关注！</div>|.
        APPEND ls_user_content1 TO lt_user_content1.
        CLEAR ls_ztint_ven_user.
      ENDLOOP.
    ENDIF.
  ENDIF.
  "售后技术
  IF es_afinf-newtype EQ 'cassmallQA'.
    SELECT a~regionid,a~regionname,a~cityid,a~cityname INTO TABLE @DATA(lt_city) FROM ztint_area_city AS a
    INNER JOIN ztint_cus_inf AS i ON a~provincegeoid = i~provincegeoid AND a~citygeoid EQ i~citygeoid AND a~countygeoid = i~countygeoid
    AND i~companyid = @ls_wo_header-company AND i~companyid NE ''.
    IF lt_city IS INITIAL.
      SELECT a~regionid,a~regionname,a~cityid,a~cityname INTO TABLE @lt_city FROM ztint_area_city AS a
      INNER JOIN ztint_cus_inf AS i ON a~provincegeoid = i~provincegeoid AND a~citygeoid EQ i~citygeoid
      AND i~companyid = @ls_wo_header-company AND i~companyid NE ''.
      IF lt_city IS INITIAL.
        SELECT a~regionid,a~regionname,a~cityid,a~cityname INTO TABLE @lt_city FROM ztint_area_city AS a
        INNER JOIN ztint_cus_inf AS i ON a~provincegeoid = i~provincegeoid
        AND i~companyid = @ls_wo_header-company AND i~companyid NE ''.
      ENDIF.
    ENDIF.
    IF lt_city IS NOT INITIAL.
      REFRESH: lt_deptlist,lt_dept_all.
      READ TABLE lt_city INTO DATA(ls_city) INDEX 1.
      SELECT SINGLE companyid,companyname INTO @DATA(ls_company) FROM ztint_team_targe
      WHERE regionid = @ls_city-regionid AND companyid NE '' AND yearmonth = @lv_yearmonth.
      IF ls_company-companyid IS NOT INITIAL.
        CALL FUNCTION 'Z_FMINT_GET_SON_DEPT'
          EXPORTING
            iv_deptid   = ls_company-companyid
          TABLES
            ot_deptlist = lt_deptlist.
        IF lt_deptlist IS NOT INITIAL.
          APPEND LINES OF lt_deptlist TO lt_dept_all.
        ENDIF.
        IF lt_dept_all IS NOT INITIAL.
          DATA lr_zposition TYPE RANGE OF ztint_user_inf-zposition.
          lr_zposition = VALUE #( ( sign = 'I' option = 'CP' low = '*售后*' ) ).
          SELECT DISTINCT a~userid,a~username,a~zposition INTO TABLE @DATA(lt_shuser) FROM ztint_user_inf AS a
          INNER JOIN ztint_user_dept AS b ON a~userid = b~userid AND b~isstill EQ 'X'
          INNER JOIN ztint_dept AS c ON b~deptid = b~deptid
          FOR ALL ENTRIES IN @lt_dept_all
          WHERE b~deptid = @lt_dept_all-deptid AND c~isstill EQ 'X'
          AND a~zposition IN @lr_zposition.
        ENDIF.
        LOOP AT  lt_shuser INTO DATA(ls_shuser).
          CLEAR ls_ztint_wo_nouser.
          ls_ztint_wo_nouser-woid = ls_wo_header-woid.
          ls_ztint_wo_nouser-noteid = lv_noteid.
          ls_ztint_wo_nouser-userid = ls_shuser-userid.
          ls_ztint_wo_nouser-usertype = '3'."抄送人
          ls_ztint_wo_nouser-zcrt_bdate = ls_wo_header-zcrt_bdate.
          ls_ztint_wo_nouser-zcrt_btime = ls_wo_header-zcrt_btime.
          ls_ztint_wo_nouser-zcrt_uname = ls_wo_header-zcrt_uname.
          ls_ztint_wo_nouser-zupd_bdate = ls_wo_header-zcrt_bdate.
          ls_ztint_wo_nouser-zupd_btime = ls_wo_header-zcrt_btime.
          ls_ztint_wo_nouser-zupd_uname = ls_wo_header-zcrt_uname.
          APPEND ls_ztint_wo_nouser TO lt_ztint_wo_nouser.

          CLEAR ls_user_content.
          ls_user_content-userid = ls_shuser-userid.
          ls_user_content-content = ' <font COLOR=#FF8C00>开思助手</font> \n' && '##### **平台申诉工单号: ' && ls_wo_header-woid && '有抄送给您，请关注！** \n'.
          lv_date1 = |{ sy-datum+0(4) }年{ sy-datum+4(2) }月{ sy-datum+6(2) }日|.
          lv_time1  = |{ sy-uzeit TIME = ISO }|.
          CONCATENATE '#####' lv_date1 lv_time1  INTO lv_tims SEPARATED BY ' '.
          ls_user_content-content = ls_user_content-content &&  ' \n'  && lv_tims.
          ls_user_content-orderno = ls_wo_header-woid.
          ls_user_content-firstcatgory = '工单中心'.
          ls_user_content-secondcatgory = '工单抄送'.
          CONCATENATE lv_date1 lv_time1  INTO lv_tims SEPARATED BY ' '.
          ls_user_content-wxcontent = |<div class=\\"gray\\">{ lv_date1 } { lv_time1  }</div>  <div class=\\"normal\\">平台申诉工单号:{ ls_wo_header-woid }'有抄送给您，请关注！</div>|.
          APPEND ls_user_content1 TO lt_user_content1.
          CLEAR ls_shuser.
        ENDLOOP.
      ENDIF.
    ENDIF.
  ENDIF.
  REFRESH: lt_dept_all,lt_deptlist.

*  "开思严选工单抄送给指定人
*  SELECT SINGLE ksbsorder INTO @DATA(lv_ksbsorder) FROM zticec_order_h WHERE orderid = @ls_wo_header-billno.
  IF lv_ksbsorder IS NOT INITIAL.
    SELECT * FROM ztint_appeal_csc INTO TABLE @DATA(lt_ksbsorder_csc) WHERE zoneid = 'F2BKSBSORDER'.
    IF sy-subrc EQ 0.
      LOOP AT lt_ksbsorder_csc INTO DATA(ls_ksbsorder_csc).
        CLEAR ls_ztint_wo_nouser.
        ls_ztint_wo_nouser-woid = ls_wo_header-woid.
        ls_ztint_wo_nouser-noteid = lv_noteid.
        ls_ztint_wo_nouser-userid = ls_ksbsorder_csc-userid.
        ls_ztint_wo_nouser-usertype = '3'."抄送人
        ls_ztint_wo_nouser-zcrt_bdate = ls_wo_header-zcrt_bdate.
        ls_ztint_wo_nouser-zcrt_btime = ls_wo_header-zcrt_btime.
        ls_ztint_wo_nouser-zcrt_uname = ls_wo_header-zcrt_uname.
        ls_ztint_wo_nouser-zupd_bdate = ls_wo_header-zcrt_bdate.
        ls_ztint_wo_nouser-zupd_btime = ls_wo_header-zcrt_btime.
        ls_ztint_wo_nouser-zupd_uname = ls_wo_header-zcrt_uname.
        APPEND ls_ztint_wo_nouser TO lt_ztint_wo_nouser.

        CLEAR ls_user_content.
        ls_user_content-userid = ls_ksbsorder_csc-userid.
        ls_user_content-content = ' <font color=#FF8C00>开思助手</font> \n' && '##### **平台申诉工单号: ' && ls_wo_header-woid && '有抄送给您，请关注！** \n'.
        lv_date1 = |{ sy-datum+0(4) }年{ sy-datum+4(2) }月{ sy-datum+6(2) }日|.
        lv_time1  = |{ sy-uzeit TIME = ISO }|.
        CONCATENATE '#####' lv_date1 lv_time1  INTO lv_tims SEPARATED BY ' '.
        ls_user_content-content = ls_user_content-content &&  ' \n'  && lv_tims.
        ls_user_content-orderno = ls_wo_header-woid.
        ls_user_content-firstcatgory = '工单中心'.
        ls_user_content-secondcatgory = '工单抄送'.
        CONCATENATE lv_date1 lv_time1  INTO lv_tims SEPARATED BY ' '.
        ls_user_content-wxcontent = |<div CLASS=\\"gray\\">{ lv_date1 } { lv_time1  }</div>  <div class=\\"normal\\">平台申诉工单号:{ ls_wo_header-woid }'有抄送给您，请关注！</div>|.
        APPEND ls_user_content1 TO lt_user_content1.
        CLEAR ls_shuser.
      ENDLOOP.
    ENDIF.
  ENDIF.

  IF es_afinf-packageimgpath IS NOT INITIAL .
    REFRESH lt_url.
    SPLIT es_afinf-packageimgpath AT '.' INTO TABLE lt_url.
    CLEAR lv_line.
    lv_line = lines( lt_url ).
    "fl 外包装全景图
    CLEAR ls_ztint_wo_fl.
    TRY .
        ls_ztint_wo_fl-media_id = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
      CATCH cx_uuid_error.
    ENDTRY.
    ls_ztint_wo_fl-filename = '外包装全景图' && '.' && lt_url[ lv_line ].
    ls_ztint_wo_fl-type = 'picture '.
    ls_ztint_wo_fl-url = es_afinf-packageimgpath.
    ls_ztint_wo_fl-zcrt_bdate = sy-datum.
    ls_ztint_wo_fl-zcrt_btime = sy-uzeit.
    ls_ztint_wo_fl-zcrt_uname = sy-uname.
    APPEND ls_ztint_wo_fl TO lt_ztint_wo_fl.

    CLEAR ls_ztint_wo_file.
    ls_ztint_wo_file-woid = ls_wo_header-woid.
    ls_ztint_wo_file-media_id = ls_ztint_wo_fl-media_id.
    ls_ztint_wo_file-noteid = lv_noteid.
    ls_ztint_wo_file-zcrt_bdate = sy-datum.
    ls_ztint_wo_file-zcrt_btime = sy-uzeit.
    ls_ztint_wo_file-zcrt_uname = sy-uname.
    APPEND ls_ztint_wo_file TO lt_ztint_wo_file.
  ENDIF.

  IF  es_afinf-detailimgpath IS NOT INITIAL.
    REFRESH lt_url.
    SPLIT es_afinf-detailimgpath AT '.' INTO TABLE lt_url.
    CLEAR lv_line.
    lv_line = lines( lt_url ).
    "fl 问题部位特写
    CLEAR ls_ztint_wo_fl.
    TRY .
        ls_ztint_wo_fl-media_id = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
      CATCH cx_uuid_error.
    ENDTRY.
    ls_ztint_wo_fl-filename = '问题部位特写' && '.' && lt_url[ lv_line ].
    ls_ztint_wo_fl-type = 'picture '.
    ls_ztint_wo_fl-url = es_afinf-detailimgpath.
    ls_ztint_wo_fl-zcrt_bdate = sy-datum.
    ls_ztint_wo_fl-zcrt_btime = sy-uzeit.
    ls_ztint_wo_fl-zcrt_uname = sy-uname.
    APPEND ls_ztint_wo_fl TO lt_ztint_wo_fl.
    CLEAR ls_ztint_wo_file.
    ls_ztint_wo_file-woid = ls_wo_header-woid.
    ls_ztint_wo_file-media_id = ls_ztint_wo_fl-media_id.
    ls_ztint_wo_file-noteid = lv_noteid.
    ls_ztint_wo_file-zcrt_bdate = sy-datum.
    ls_ztint_wo_file-zcrt_btime = sy-uzeit.
    ls_ztint_wo_file-zcrt_uname = sy-uname.
    APPEND ls_ztint_wo_file TO lt_ztint_wo_file.
  ENDIF.

  IF es_afinf-antifakeimgpath IS NOT INITIAL.
    REFRESH lt_url.
    SPLIT es_afinf-antifakeimgpath AT '.' INTO TABLE lt_url.
    CLEAR lv_line.
    lv_line = lines( lt_url ).
    "fl 问题部位特写
    CLEAR ls_ztint_wo_fl.
    TRY .
        ls_ztint_wo_fl-media_id = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
      CATCH cx_uuid_error.
    ENDTRY.
    ls_ztint_wo_fl-filename = '开思售后标签' && '.' && lt_url[ lv_line ].
    ls_ztint_wo_fl-type = 'picture '.
    ls_ztint_wo_fl-url = es_afinf-antifakeimgpath.
    ls_ztint_wo_fl-zcrt_bdate = sy-datum.
    ls_ztint_wo_fl-zcrt_btime = sy-uzeit.
    ls_ztint_wo_fl-zcrt_uname = sy-uname.
    APPEND ls_ztint_wo_fl TO lt_ztint_wo_fl.

    CLEAR ls_ztint_wo_file.
    ls_ztint_wo_file-woid = ls_wo_header-woid.
    ls_ztint_wo_file-media_id = ls_ztint_wo_fl-media_id.
    ls_ztint_wo_file-noteid = lv_noteid.
    ls_ztint_wo_file-zcrt_bdate = sy-datum.
    ls_ztint_wo_file-zcrt_btime = sy-uzeit.
    ls_ztint_wo_file-zcrt_uname = sy-uname.
    APPEND ls_ztint_wo_file TO lt_ztint_wo_file.
  ENDIF.

  "其他图片
  IF as_datail-as-attachments IS INITIAL.
    DATA(lv_staus_line) = lines( as_datail-statusinfo ).
    READ TABLE as_datail-statusinfo INTO DATA(ls_statusinfo) INDEX lv_staus_line."WITH KEY asstatusid = es_afinf-statusid BINARY SEARCH.
    DATA(lt_afimg) = ls_statusinfo-attachments.
  ELSE.
    lt_afimg = as_datail-as-attachments.
  ENDIF.

  LOOP AT lt_afimg INTO DATA(ls_afimg).
    REFRESH lt_url.
    CLEAR ls_ztint_wo_fl.
    TRY .
        ls_ztint_wo_fl-media_id = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).
      CATCH cx_uuid_error.
    ENDTRY.
    "ls_ztint_wo_fl-filename = '问题部位特写' && '.' && lt_url[ lv_line ].
    ls_ztint_wo_fl-filename = ls_afimg-typename.
*      ls_ztint_wo_fl-type =  COND #( WHEN ls_afimg-attachmentformat EQ 'video' THEN 'video' ELSE  'picture' ).
*      ls_ztint_wo_fl-url = COND #( WHEN ls_afimg-attachmentformat EQ 'video' THEN ls_afimg-attachmentpath ELSE ls_afimg-imgpath ).
    ls_ztint_wo_fl-type =  COND #( WHEN ls_afimg-format EQ 'video' THEN 'video' ELSE  'picture' ).
    ls_ztint_wo_fl-url = COND #( WHEN ls_afimg-format EQ 'video' THEN ls_afimg-path ELSE ls_afimg-thumbnailpath ).
    ls_ztint_wo_fl-zcrt_bdate = sy-datum.
    ls_ztint_wo_fl-zcrt_btime = sy-uzeit.
    ls_ztint_wo_fl-zcrt_uname = sy-uname.
    APPEND ls_ztint_wo_fl TO lt_ztint_wo_fl.

    CLEAR ls_ztint_wo_file.
    ls_ztint_wo_file-woid = ls_wo_header-woid.
    ls_ztint_wo_file-media_id = ls_ztint_wo_fl-media_id.
    ls_ztint_wo_file-noteid = lv_noteid.
    ls_ztint_wo_file-zcrt_bdate = sy-datum.
    ls_ztint_wo_file-zcrt_btime = sy-uzeit.
    ls_ztint_wo_file-zcrt_uname = sy-uname.
    APPEND ls_ztint_wo_file TO lt_ztint_wo_file.
    CLEAR ls_afimg.
  ENDLOOP.
  DATA:ls_ztint_wo_owner TYPE ztint_wo_owner.
  ls_ztint_wo_owner-woid = ls_wo_header-woid.
  ls_ztint_wo_owner-owner = '6'.
  ls_ztint_wo_owner-zcrt_bdate = sy-datum.
  ls_ztint_wo_owner-zcrt_btime = sy-uzeit.
  ls_ztint_wo_owner-zupd_bdate = sy-datum.
  ls_ztint_wo_owner-zupd_btime = sy-uzeit.
  "更新表
  DATA(ls_log) = VALUE ztint_wo_action( woid = ls_wo_header-woid guid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
        action = 'CREATE' actiondesc = '新建工单' username = ls_wo_header-zcrt_uname node = 'N0000'
        content = ls_wo_header-zcrt_uname && '发起了申诉工单：' && ls_wo_header-woid
        zcrt_uname = sy-uname zcrt_bdate = sy-datum zcrt_btime = sy-uzeit ).
  "创建时写入BI更新状态表
  DATA:lv_hour TYPE zde_intamount.
  "根据工单问题类型及紧急程度获取应完成时间
  SELECT SINGLE * FROM ztint_wo_times INTO @DATA(ls_times) WHERE ztype = @ls_wo_header-type.
  IF sy-subrc EQ 0.
    lv_hour = COND #( WHEN ls_wo_header-degree = '1' THEN ls_times-zuhour3
    WHEN ls_wo_header-degree = '2' THEN ls_times-zuhour2
    WHEN ls_wo_header-degree = '3' THEN ls_times-zuhour1 ).
    IF lv_hour IS NOT INITIAL.
      zcl_cassint_formatter=>get_times( EXPORTING iv_date = ls_wo_header-zcrt_bdate iv_time = ls_wo_header-zcrt_btime hours = lv_hour
      IMPORTING ev_date = DATA(lv_needdate) ev_time = DATA(lv_needtime) ).
    ENDIF.
  ENDIF.
  DATA(ls_wostatus) = VALUE ztint_wo_status( woid = ls_wo_header-woid needdate = lv_needdate needtime = lv_needtime
        actdate = lv_needdate acttime = lv_needtime
        companyid = ls_wo_header-company
        wostatus = ls_wo_header-wostatus degree = ls_wo_header-degree
        zcrt_uname = sy-uname zcrt_bdate = sy-datum zcrt_btime = sy-uzeit
        zupd_bdate = sy-datum zupd_btime = sy-uzeit zupd_uname = sy-uname ).
  "判断对应订单是否存在未完结风险件服务工单,存在则需将添加售后中节点
  lv_reason = COND #( WHEN es_afinf-statusid EQ 'APPEAL_CREATE'  THEN '客户在电商平台发起劣品申诉'
  WHEN es_afinf-statusid EQ 'AFTERSALE_CREATE' AND es_afinf-newtype EQ 'sellerQA' THEN '客户在电商平台发起商家质保转申诉'
  WHEN es_afinf-statusid EQ 'RETURN_CREATE' AND es_afinf-newtype EQ 'orderReturn' THEN '客户在电商平台发起退货转申诉'
  WHEN es_afinf-statusid EQ 'FAST_COMPENSATION_CREATE' THEN '客户在电商平台发起快处快赔'
  WHEN es_afinf-statusid EQ 'EASY_RETURN_CREATE' THEN '客户在电商平台发起无忧退货').
  lo_hrisk->set_hriskwo_shz( orderid = EXACT string( ls_wo_header-billno ) et_aforder = et_aforder
  asid = EXACT string( ls_wo_header-asid ) reason = lv_reason ).
  es_afinf-woid = ls_wo_header-woid.
  es_afinf-zupd_bdate = sy-datum.
  es_afinf-zupd_btime = sy-uzeit.

  "同步工单次数至会员
  IF lv_oldwo IS INITIAL.
    SELECT woid,wotimes INTO TABLE @DATA(lt_cuswo) FROM ztint_wo_header
          WHERE company = @ls_wo_header-company AND isdelete NE 'X' AND wotimes NE '' AND  isdraft EQ ''.
    SORT lt_cuswo BY wotimes DESCENDING.
    READ TABLE lt_cuswo INTO DATA(ls_cuswo) INDEX 1.
    ls_wo_header-wotimes = ls_cuswo-wotimes + 1.
    ls_custag = VALUE #(  businesstype = 'CASS_AIDE'  conditiontype = 'WORKSHEET_TIMES'
    companyid  = ls_wo_header-company externalid = ls_wo_header-woid
    number = ls_wo_header-wotimes  operator = ls_wo_header-zcrt_uname ).
    lo_user->save_companytags( EXPORTING es_custag = ls_custag IMPORTING ev_msg = DATA(lv_msg1) ).
  ENDIF.

  IF ls_wo_header IS NOT INITIAL.
    MODIFY ztint_wo_header FROM ls_wo_header.
    MODIFY ztint_wo_status FROM ls_wostatus.
  ENDIF.
  IF lt_wo_item IS NOT INITIAL.
    MODIFY ztint_wo_item FROM TABLE lt_wo_item.
  ENDIF.
  IF lt_ztint_wo_flow IS NOT INITIAL.
    MODIFY ztint_wo_flow FROM TABLE lt_ztint_wo_flow.
  ENDIF.
  IF lt_ztint_wo_note IS NOT INITIAL.
    MODIFY ztint_wo_note FROM TABLE lt_ztint_wo_note.
  ENDIF.
  IF lt_ztint_wo_nouser IS NOT INITIAL.
    MODIFY ztint_wo_nouser FROM TABLE lt_ztint_wo_nouser.
  ENDIF.
  IF lt_ztint_wo_fl IS NOT INITIAL.
    MODIFY ztint_wo_fl   FROM TABLE lt_ztint_wo_fl.
  ENDIF.
  IF lt_ztint_wo_file IS NOT INITIAL.
    MODIFY ztint_wo_file FROM TABLE lt_ztint_wo_file.
  ENDIF.
  MODIFY ztint_wo_action FROM ls_log.
  MODIFY ztint_wo_owner FROM ls_ztint_wo_owner.
  IF es_afinf IS NOT INITIAL.
    MODIFY zticec_af_inf FROM es_afinf.
  ENDIF.
*****
*****  "20240625 空军自动领取的工单要同步消息到平台
*****  DATA(lo_af) = NEW zcl_cassint_aftersales( ).
*****  DATA:lv_as_status_id        TYPE string,
*****       lv_ispaidcoin          TYPE string,
*****       lv_compensationmessage TYPE string,
*****       lv_platformdesc        TYPE string.
*****  IF lv_getted IS NOT INITIAL.
****** 申诉工单订单确认领取的时候发送消息给客户,
*****    IF ls_wo_header-wotype = 'R'.
*****      lv_handletime = |{ sy-datum DATE = ISO } { sy-uzeit TIME = ISO }|.
*****      lv_username = sy-uname.
*****      lv_asid = ls_wo_header-asid.
******add 权益工单
*****      CLEAR:lv_as_status_id,lv_repairdesc,lv_platformdesc.
*****      IF  ls_wo_header-righttype EQ  'EASY_RETURN'."无忧退货
*****        lv_as_status_id = 'EASY_RETURN_ACCEPT'.
*****        lv_repairdesc = |开思已收到您的售后申请|.
*****      ELSEIF ls_wo_header-righttype EQ 'FAST_COMPENSATION' OR ls_wo_header-wochdtype EQ 'Q'."快处快赔、质保工单
*****        lv_as_status_id = 'FAST_COMPENSATION_ACCEPT'.
*****        lv_repairdesc = |开思已收到您的售后申请|.
*****        IF ls_wo_header-woroottype EQ 'Q1' OR ls_wo_header-woroottype EQ 'Q2'.
*****          lv_platformdesc = |开思已收到客户的售后申请|.
*****        ENDIF.
*****      ELSEIF ls_wo_header-woroottype EQ 'T1' ."退货转申诉
*****        lv_as_status_id = COND #( WHEN es_afinf-type EQ  'returnAftersale' THEN  'APPEAL_ACCEPT' ELSE 'AFTERSALE_PROCESSING' ).
*****      ELSEIF ls_wo_header-woroottype EQ 'S'."商家质保申诉
*****        lv_as_status_id =  'AFTERSALE_PROCESSING'.
*****      ELSE.
*****        lv_as_status_id = 'APPEAL_ACCEPT'.
*****      ENDIF.
*****      IF lv_usernewapi EQ 'X' OR "新单API同步信息
*****      ( es_afinf-type = 'cassmallQA' OR "新质保
*****      es_afinf-type = 'sellerQA' OR "商家质保
*****      es_afinf-type = 'orderReturn' )."新退货
*****        ls_asinf = VALUE #( asid = ls_wo_header-asid aftersaleform-asstatusid = lv_as_status_id
*****        handletime = lv_handletime aftersaleform-repairdesc = lv_repairdesc
*****        aftersaleform-ispaidcoin = lv_ispaidcoin aftersaleform-compensationmessage = lv_compensationmessage
*****        aftersaleform-platformdesc = lv_platformdesc handleusername = lv_username ).
*****        DATA(ls_afresult) = lo_asapi->update_aftersale_status( EXPORTING es_asinf = ls_asinf ).
*****        IF ls_afresult-errormessage IS NOT INITIAL.
*****          lv_orderno = ls_wo_header-woid.
*****          lo_af->save_wo_log( EXPORTING event_id = 'GETWOSTATUSUPADTE' event_desc = '工单领取同步至平台' status = 'E'
*****            orderno = lv_orderno key_value1 = lv_asid message = ls_afresult-errormessage  ).
*****        ENDIF.
*****      ELSE.
*****        CALL FUNCTION 'Z_FMINT_SEND_MESSAGE_CUS'
*****          IN UPDATE TASK
*****          EXPORTING
*****            as_id        = lv_asid
*****            as_status_id = lv_as_status_id
*****            handle_time  = lv_handletime
*****            repairdesc   = lv_repairdesc
*****            iv_username  = lv_username.
*****      ENDIF.
*****
*****    ENDIF.
*****  ENDIF.
*****  "20240625 空军自动领取的工单要同步消息到平台

  "消息推送
  IF lr_userid IS NOT INITIAL.
    DELETE lt_user_content1 WHERE userid IN lr_userid.
  ENDIF.
  SORT lt_user_content1 BY userid.
  DELETE ADJACENT DUPLICATES FROM lt_user_content1 COMPARING userid.

  lv_msg_type1 = 'action_card'.
  lv_title1 =  '开思助手工单中心'.
  IF lt_user_content1 IS NOT INITIAL.
    lv_url1 =  '#/work' && '/' && ls_wo_header-woid  && '?isMessagePush=true' .
    CALL FUNCTION 'Z_FMINT_WO_PUSH'
      IN UPDATE TASK
      EXPORTING
        iv_title    = lv_title
        iv_msg_type = lv_msg_type
        iv_url      = lv_url
      TABLES
        it_userlist = lt_user_content1.
  ENDIF.

  IF lt_userlist_workrecord IS NOT INITIAL.
    lv_title1 =  '开思助手-工单处理'.
    lv_url1 =  '#/work' && '/' && ls_wo_header-woid  && '?isMessagePush=true' .
    lv_log_msg1 = '申诉工单待办推送'.
    lv_fnam = 'INTWONOTE'.
    lv_value = lv_noteid_next .
    CALL FUNCTION 'Z_FMINT_WO_PUSH_WORKRECORD'
      IN UPDATE TASK
      EXPORTING
        iv_title        = lv_title
        iv_url          = lv_url
        iv_log_msg      = lv_log_msg
        it_formitemlist = lt_formitemlist
        iv_update       = ''
        iv_fnam         = lv_fnam
        iv_value        = lv_value
      TABLES
        it_userlist     = lt_userlist_workrecord.
  ENDIF.

  "售后领取工单的需要推消息到群
  IF lv_shown_flg IS NOT INITIAL AND lv_robotkey IS NOT INITIAL.
    DATA:ls_sendmsg TYPE zcl_corpweixin_api=>ts_msg,
         lv_wourl   TYPE string,
         lv_ordurl  TYPE string.
    DATA:lt_noticeuser TYPE STANDARD TABLE OF ztint_user_inf-userid.
    "构建机器人消息
    SELECT SINGLE mobileurl FROM zticerp_ding_sk AS a INNER JOIN zticerp_ding_gid AS g
    ON a~gentid = g~gentid INTO @DATA(lv_hostname) WHERE g~fname = 'CASSINTQYWX'.
    lv_wourl = |{ lv_hostname }#/work/{ ls_wo_header-woid }?isMessagePush=true|.
    lv_ordurl = |{ lv_hostname }#/order/{ ls_wo_header-billno }?isMessagePush=true|.
    ls_sendmsg-msgtype = 'markdown'.
    ls_sendmsg-content = |<font color=\\"warning\\">质保工单待领取</font> \\n|.
    "获取@负责人
    SPLIT ls_noticeuser-useridstr AT ',' INTO TABLE lt_noticeuser.
    IF lt_noticeuser IS NOT INITIAL.
      SELECT qywxuserid INTO TABLE @DATA(lt_shnotice) FROM ztint_user_inf
       FOR ALL ENTRIES IN @lt_noticeuser
        WHERE userid = @lt_noticeuser-table_line  AND isstill = 'X'.
    ELSE.
      SPLIT ls_noticeuser-lastuseridstr AT ',' INTO TABLE lt_noticeuser.
      IF lt_noticeuser IS NOT INITIAL.
        SELECT qywxuserid INTO TABLE @lt_shnotice FROM ztint_user_inf
         FOR ALL ENTRIES IN @lt_noticeuser
          WHERE userid = @lt_noticeuser-table_line(32) AND isstill = 'X'.
      ENDIF.
    ENDIF.
    IF lt_shnotice IS NOT INITIAL.
      DATA(lv_qwuserstr) = REDUCE string( INIT x TYPE string FOR wa IN lt_shnotice
                                            NEXT x = COND #( WHEN x IS INITIAL THEN |<@{ wa-qywxuserid }>|
                                                             ELSE |{ x },<@{ wa-qywxuserid }>| ) ).
      ls_sendmsg-content = ls_sendmsg-content && |>售后人员：{ lv_qwuserstr } \\n |.
    ENDIF.

    SELECT SINGLE username INTO @DATA(lv_bdname) FROM ztint_cus_inf AS i
    INNER JOIN ztint_cus_user AS u ON i~cusid = u~cusid AND u~usertype = '1' AND u~isdelete = ''
    INNER JOIN ztint_user_inf AS ui ON u~userid = ui~userid AND ui~isstill = 'X'
    WHERE i~companyid = @ls_wo_header-company.

    ls_sendmsg-content = ls_sendmsg-content &&
                         |>质保工单：[{ ls_wo_header-woid }]({ lv_wourl }) \\n | &&
                         |>订单：[{ ls_wo_header-billno }]({ lv_ordurl }) \\n | &&
                         |>车辆品牌：{ ls_wo_header-carbrandname } \\n | &&
                         |>配件：{ lv_parts } \\n | &&
                         |>供应商：{ ls_wo_header-productstorename } \\n  | &&
                         |>维修厂：{ ls_wo_header-companyname  } \\n  | &&
                         |>申诉人：{ ls_wo_header-userloginname } / { ls_wo_header-cellphone } \\n  | &&
                         COND #( WHEN lv_bdname IS NOT INITIAL THEN |>BD:{ lv_bdname } \\n  | ) &&
                         |>时间：{ ls_wo_header-zcrt_bdate DATE = ISO } { ls_wo_header-zcrt_btime TIME = ISO } \\n| &&
                         |>[立即处理]({ lv_wourl })|.

    DATA(lo_notice) = NEW zcl_cassint_notice( ).
    lo_notice->set_robot_message(
      EXPORTING iv_key  = EXACT string( lv_robotkey ) sendmsg = ls_sendmsg ).
  ENDIF.
***  IF lt_msglist IS NOT INITIAL.
***    CALL FUNCTION 'Z_FMINT_SAVE_MSGLIST'
***      IN UPDATE TASK
***      TABLES
***        it_msglist = lt_msglist.
***  ENDIF.
  COMMIT WORK AND WAIT.

ENDMETHOD.
