class ZCL_CASSINT_NOTICE definition
  public
  final
  create public .

public section.

  methods GET_DEPT_ROBOT_BY_ADDRESS
    importing
      value(IV_BUSITYPE) type STRING
      value(IS_ADDRESS) type ZSINT_AREA_CITY
    returning
      value(RS_ROBOTKEY) type STRING .
  methods GET_DEPT_ROBOT_BY_USER
    importing
      value(IV_BUSITYPE) type STRING
      value(USERID) type STRING
    returning
      value(RS_ROBOTKEY) type STRING .
  methods SET_ROBOT_MESSAGE
    importing
      value(IV_KEY) type STRING
      value(SENDMSG) type ZCL_CORPWEIXIN_API=>TS_MSG .
  methods UPLOAD_ROBOT_TEMPORARY_MEDIA
    importing
      value(IV_FILENAME) type STRING
      value(IV_FILETYPE) type STRING optional
      value(IV_VALUE) type XSTRING
      value(IV_CONTENTTYPE) type STRING optional
      value(IV_URLKEY) type STRING
    returning
      value(MEDIA) type ZCL_CORPWEIXIN_API=>TS_MEDIARET .
protected section.
private section.
ENDCLASS.



CLASS ZCL_CASSINT_NOTICE IMPLEMENTATION.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CASSINT_NOTICE->GET_DEPT_ROBOT_BY_ADDRESS
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_BUSITYPE                    TYPE        STRING
* | [--->] IS_ADDRESS                     TYPE        ZSINT_AREA_CITY
* | [<-()] RS_ROBOTKEY                    TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
METHOD get_dept_robot_by_address.
  CHECK is_address IS NOT INITIAL.

  SELECT SINGLE areaid FROM ztint_area_city INTO @DATA(lv_areaid)
        WHERE provincegeoid = @is_address-provincegeoid AND citygeoid = @is_address-citygeoid
        AND countygeoid = @is_address-countygeoid.
  IF lv_areaid = 'ZCN-44-16-10-1'."中山网格按网格ID找机器人
    SELECT SINGLE robotkey FROM ztint_dept_robot INTO rs_robotkey
    WHERE busitype = iv_busitype AND deptid = lv_areaid.
  ELSE."不为中山网格再按连队找机器人
    SELECT SINGLE regionid FROM ztint_area_city INTO @DATA(lv_regionid)
          WHERE provincegeoid = @is_address-provincegeoid AND citygeoid = @is_address-citygeoid
          AND countygeoid = @is_address-countygeoid AND villageoid = @is_address-villagegeoid.
    CHECK lv_regionid IS NOT INITIAL.

    SELECT SINGLE robotkey FROM ztint_dept_robot INTO rs_robotkey
    WHERE busitype = iv_busitype AND deptid = lv_regionid.
  ENDIF.

ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CASSINT_NOTICE->GET_DEPT_ROBOT_BY_USER
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_BUSITYPE                    TYPE        STRING
* | [--->] USERID                         TYPE        STRING
* | [<-()] RS_ROBOTKEY                    TYPE        STRING
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD get_dept_robot_by_user.
    TYPES:BEGIN OF ts_dept,
            deptid TYPE ztint_dept-deptid,
          END OF ts_dept.
    DATA:lt_dept TYPE STANDARD TABLE OF ts_dept,
         ls_dept TYPE ts_dept.
    "根据人的所有部门
    SELECT userid,deptid FROM ztint_user_dept INTO TABLE @DATA(lt_userdept)
      WHERE userid = @userid AND isstill = 'X'.
    LOOP AT lt_userdept INTO DATA(ls_userdept).
      ls_dept-deptid = ls_userdept-deptid.
      APPEND ls_dept TO lt_dept.

      DO.
        SELECT SINGLE parentid INTO @DATA(lv_parent) FROM ztint_dept
          WHERE deptid = @ls_dept-deptid AND isstill = 'X'.
        IF sy-subrc NE 0 OR lv_parent EQ '1'.
          EXIT.
        ELSE.
          ls_dept-deptid = lv_parent.
          APPEND ls_dept TO lt_dept.
        ENDIF.
      ENDDO.
    ENDLOOP.
    SORT lt_dept BY deptid.
    DELETE ADJACENT DUPLICATES FROM lt_dept COMPARING ALL FIELDS.

    "查找部门机器人
    "查找部门机器人
    SELECT * FROM ztint_dept_robot INTO TABLE @DATA(lt_robot)
      FOR ALL ENTRIES IN @lt_dept
     WHERE busitype = @iv_busitype AND deptid = @lt_dept-deptid.
    CHECK sy-subrc EQ 0.

    READ TABLE lt_robot INTO DATA(ls_robot) INDEX 1.
    rs_robotkey = ls_robot-robotkey.

  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CASSINT_NOTICE->SET_ROBOT_MESSAGE
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_KEY                         TYPE        STRING
* | [--->] SENDMSG                        TYPE        ZCL_CORPWEIXIN_API=>TS_MSG
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD set_robot_message.
    DATA(lo_qywx) = NEW zcl_corpweixin_api( appname = 'CASSINTQYWX' destination = 'QYWX' ).
    lo_qywx->robot_push_message( iv_key = iv_key sendmsg = sendmsg ).

  ENDMETHOD.


* <SIGNATURE>---------------------------------------------------------------------------------------+
* | Instance Public Method ZCL_CASSINT_NOTICE->UPLOAD_ROBOT_TEMPORARY_MEDIA
* +-------------------------------------------------------------------------------------------------+
* | [--->] IV_FILENAME                    TYPE        STRING
* | [--->] IV_FILETYPE                    TYPE        STRING(optional)
* | [--->] IV_VALUE                       TYPE        XSTRING
* | [--->] IV_CONTENTTYPE                 TYPE        STRING(optional)
* | [--->] IV_URLKEY                      TYPE        STRING
* | [<-()] MEDIA                          TYPE        ZCL_CORPWEIXIN_API=>TS_MEDIARET
* +--------------------------------------------------------------------------------------</SIGNATURE>
  METHOD upload_robot_temporary_media.
    DATA(lo_qywx) = NEW zcl_corpweixin_api( appname = 'CASSINTQYWX'  destination = 'QYWX' ).
    media = lo_qywx->robot_upload_temporary_media(
          iv_filename    = iv_filename
          iv_filetype    = 'file'
          iv_contenttype = iv_contenttype
          iv_value       = iv_value
          iv_urlkey = iv_urlkey ).
  ENDMETHOD.
ENDCLASS.