  METHOD check_fxrisk_rule.
    TYPES:
      BEGIN OF ty_customergroup,
        companyid       TYPE string,
        customergroupid TYPE string,
        result          TYPE string,
      END OF ty_customergroup .
    DATA lt_customergroup TYPE STANDARD TABLE OF ty_customergroup WITH DEFAULT KEY.
    TYPES:BEGIN OF ty_parts,
            partsno    TYPE string,
            partsname  TYPE string,
            partsprice TYPE string,
            partsmark  TYPE string,
            partsurl   TYPE string,
          END OF ty_parts.
    DATA:lt_parts TYPE STANDARD TABLE OF ty_parts.
    DATA:ls_address TYPE zsint_area_city.
    DATA: lv_isreplace  TYPE char1, "是否替换件
          lv_replacenum TYPE string. "替换件零件号
    DATA:lv_lighthit TYPE string. "命中大灯品类

    SELECT SINGLE orderid,custcompanyid,ksbsorder,createdname,contactnumber INTO @DATA(ls_orderinf_tmp) FROM zticec_order_h WHERE orderid = @order.
    CHECK sy-subrc EQ 0.
    DATA(lv_orderid_tmp) = ls_orderinf_tmp-orderid.
    DATA(lv_companyid_tmp) = ls_orderinf_tmp-custcompanyid.
    DATA(lv_yxflag) = ls_orderinf_tmp-ksbsorder."严选标记
    DATA(lv_receive) = ls_orderinf_tmp-createdname."收货人姓名
    DATA(lv_contactnumber) = ls_orderinf_tmp-contactnumber."收货人手机号
    "同一客户同一零件号码60天内有生成过待办且该待办已处理就不推送高风险件到群里（风险单工单、待办和群消息推送同生共灭），即也不会生成风险件工单和待办
    "获取60天内该订单客户已处理的高风险件待办及其对应客户和零件号
    DATA:lv_60day_ago TYPE sy-datum.
    lv_60day_ago = sy-datum - 60.
    SELECT l~todoid,l~todokeyvalue,l~zcusid,i~partsnum,i~categoryid,i~acturalsellerprice INTO TABLE @DATA(lt_todoinf)
          FROM ztint_todo_list AS l INNER JOIN ztint_todo_class AS c ON l~todoid = c~todoid AND c~msgsecondcat = 'NEWFXWO'
          INNER JOIN ztint_cus_inf AS a ON l~zcusid = a~cusid
          INNER JOIN ztint_hriskwo_h AS h ON l~todokeyvalue = h~orderid
          INNER JOIN ztint_hriskwo_i AS i ON h~orderid = i~orderid
          WHERE l~todostatus EQ '2' AND l~isdelete = ''
          AND a~companyid = @lv_companyid_tmp
          AND l~todoeffectdate BETWEEN @lv_60day_ago AND @sy-datum.

    IF lt_todoinf[] IS NOT INITIAL.
      SORT lt_todoinf BY partsnum.
      "零件号去空格方便比较订单行项目的零件号和近60天该客户已处理高风险件待办对应的零件号是否一致
      LOOP AT lt_todoinf ASSIGNING FIELD-SYMBOL(<fs_todoinf>).
        CONDENSE <fs_todoinf>-partsnum NO-GAPS.
      ENDLOOP.

      "获取订单行项目对应的零件号
      SELECT orderid,partsnum INTO TABLE @DATA(lt_orderi)
        FROM zticec_order_i
        WHERE orderid = @order.

      "订单行项目对应的零件号去空格
      LOOP AT lt_orderi ASSIGNING FIELD-SYMBOL(<fs_orderi>).
        CONDENSE <fs_orderi>-partsnum NO-GAPS.
      ENDLOOP.

      LOOP AT lt_orderi INTO DATA(ls_orderi).
        READ TABLE lt_todoinf INTO DATA(ls_todoinf) WITH KEY partsnum = ls_orderi-partsnum BINARY SEARCH. "读到了就退出程序
        IF sy-subrc = 0.
          RETURN.
        ENDIF.
      ENDLOOP.
    ENDIF.
    "同一客户同一零件号码60天内有生成过待办且该待办已处理就不推送高风险件到群里（风险单工单、待办和群消息推送同生共灭），即也不会生成风险件工单和待办

    "调接口获取订单详情信息
    DATA(lo_order) = NEW zcl_icec_order_api( ).
    lo_order->get_order_detail_data( EXPORTING iv_orderid = order iv_showsource = 'PLATFORM'
    IMPORTING es_out = DATA(ls_detail) ev_msg = DATA(lv_msg) ).

    "剔除专属商家
    SELECT * INTO TABLE @DATA(lt_delstore) FROM ztint_del_store.
    SORT lt_delstore BY productstoreid.
    READ TABLE lt_delstore INTO DATA(ls_delstore) WITH KEY productstoreid = ls_detail-orderheader-fulfillstore-storeid BINARY SEARCH.
    IF sy-subrc EQ 0.
      EXIT.
    ENDIF.

    DELETE ls_detail-orderitems WHERE categorycode NE '11366' AND categorycode NE '10634' AND ( originalpartsnum = '' OR categorycode = ''
    OR quantity = '' OR brandid = '').
    CHECK ls_detail-orderitems IS NOT INITIAL.

    ls_address = VALUE #( provincegeoid = ls_detail-orderpostaladdress-provincegeoid
    citygeoid = ls_detail-orderpostaladdress-citygeoid
    countygeoid = ls_detail-orderpostaladdress-countygeoid
    villagegeoid = ls_detail-orderpostaladdress-villagegeoid ).
    "判断客户是否在排除客户名单内
    SELECT SINGLE value INTO @DATA(lv_customergroupid) FROM ztint_par WHERE fname = 'CUSTOMERGROUPID'.
    lt_customergroup = VALUE #( ( companyid = ls_detail-orderheader-buyer-companyid customergroupid = lv_customergroupid ) ).

    DATA(lo_user) = NEW zcl_icec_user_api( ).
    lo_user->get_customergroups_exit( EXPORTING it_customergroup = lt_customergroup  IMPORTING et_customergroup = DATA(lt_cusgroup) ).
    READ TABLE lt_cusgroup INTO DATA(ls_cusgroup) WITH KEY companyid = ls_detail-orderheader-buyer-companyid.
    CHECK ls_cusgroup-result IS INITIAL.

    "获取规则
    SELECT * FROM ztint_riskrule INTO TABLE @DATA(lt_rule) WHERE type = 'Class' AND isdelete = ''."品类规则
    SELECT * FROM ztint_riskrule APPENDING TABLE @lt_rule WHERE type = 'Parts' AND needfollow = 'X' AND isdelete = ''."保留部分重要oe，机器人推送給售后去人工跟进
    IF lt_rule IS NOT INITIAL.
      SELECT * FROM ztint_ruleext INTO TABLE @DATA(lt_ruleext)
            FOR ALL ENTRIES IN @lt_rule WHERE guid = @lt_rule-guid..
    ENDIF.
    SORT lt_rule BY guid.
    SORT lt_ruleext BY guid zgroup id.

    DATA:lv_prov                TYPE string,
         lv_city                TYPE string,
         lv_county              TYPE string,
         lv_village             TYPE string,
         lv_carbrandid          TYPE string,
         lv_carbrandname        TYPE string,
         lv_partscategorycode   TYPE string,
         lv_partscategorycodenm TYPE string,
         lv_partsmenucode4      TYPE string,
         lv_partsmenucode3      TYPE string,
         lv_partsmenucode2      TYPE string,
         lv_partsmenucode1      TYPE string,
         lv_partsnum            TYPE string,
         lv_partsno             TYPE string,
         lv_price               TYPE string,
         lv_partsbrandid        TYPE string,
         lv_partsquality        TYPE string.
    DATA:lv_carexist     TYPE c,
         lv_zoneexist    TYPE c,
         lv_qualityexist TYPE c.
    DATA:lv_hit          TYPE string,
         lv_riskremark   TYPE string,
         lv_parts_hit    TYPE string,
         lv_class_hit    TYPE string,
         lv_qualityname  TYPE string,
         lv_partsname    TYPE string,
         lv_riskurl      TYPE string,
         lv_risktypedesc TYPE string.
    DATA:ls_hriskwo_i     TYPE ztint_hriskwo_i,
         lt_hriskwo_i     TYPE STANDARD TABLE OF ztint_hriskwo_i,
         ls_hriskwo_h     TYPE ztint_hriskwo_h,
         lt_hriskwo_h     TYPE STANDARD TABLE OF ztint_hriskwo_h,
         ls_hriskwo_f     TYPE ztint_hriskwo_f,
         ls_nexthriskwo_f TYPE ztint_hriskwo_f,
         lt_hriskwo_f     TYPE STANDARD TABLE OF ztint_hriskwo_f,
         lt_risktype      TYPE STANDARD TABLE OF ztint_ruleext.
    DATA:lv_number_pre   TYPE int4,
         lv_cjnumber_pre TYPE int4,
         lv_woid         TYPE ztint_hriskwo_h-woid,
         lv_status       TYPE zde_hriskwostatus,
         lv_partsprice   TYPE p DECIMALS 2,
         lv_ordamt       TYPE p DECIMALS 2.
    "替换件相关
    DATA oe_codes TYPE string.
    DATA(lo_mdm) = NEW zcl_icec_mdm_api( ).
    DATA: lv_codeto     TYPE string, "替换号
          lv_codetrimto TYPE string. "去空格替换号

    lv_prov = ls_detail-orderpostaladdress-provincegeoid."收货地址-省
    lv_city = ls_detail-orderpostaladdress-citygeoid."收货地址-市
    lv_county = ls_detail-orderpostaladdress-countygeoid."收货地址-区
    lv_village = ls_detail-orderpostaladdress-villagegeoid.""收货地址-街道
    lv_ordamt = ls_detail-orderpayment-orderactualcurrencyamount.
    SELECT SINGLE carbrandid carbrandname INTO (lv_carbrandid,lv_carbrandname) FROM zticec_order_h
    WHERE orderid = order. "汽车品牌
    IF lv_carbrandid IS INITIAL.
      SELECT SINGLE carbrandid carbrandname INTO (lv_carbrandid,lv_carbrandname) FROM zticec_inquiry_h
      WHERE inquiryid = ls_detail-orderheader-originalsource.
    ENDIF.

    LOOP AT ls_detail-orderitems INTO DATA(ls_item).
      lv_partscategorycode = ls_item-categorycode."品类代码
      lv_partsprice = ls_item-orderitempayment-selleractualprice.
      "品类对应的四级分类目录代码
      IF lv_partscategorycode IS NOT INITIAL.
        SELECT SINGLE catalogcode categoryname INTO ( lv_partsmenucode4,lv_partscategorycodenm )
        FROM ztint_partscatal
        WHERE categorycode = lv_partscategorycode."目标代码
        lv_partsmenucode1 =   lv_partsmenucode4(1).
        lv_partsmenucode2 =   lv_partsmenucode4(3).
        lv_partsmenucode3 =   lv_partsmenucode4(5).
      ENDIF.

      lv_partsbrandid = ls_item-brandid."配件品牌
      lv_partsquality = ls_item-quality."品质

      IF lv_partscategorycode EQ '11366' OR lv_partscategorycode EQ '10634'." 发动机命中

        "获取连队
        "通过省市区街道找归属的连队
        SELECT SINGLE regionid FROM ztint_area_city INTO @DATA(lv_regionid)
              WHERE provincegeoid = @lv_prov AND citygeoid = @lv_city
              AND countygeoid = @lv_county AND villageoid = @lv_village.
*对应街道在网格表没找到对应连队就往上一级找
        IF lv_regionid IS INITIAL.
          SELECT SINGLE regionid FROM ztint_area_city INTO @lv_regionid
          WHERE provincegeoid = @lv_prov AND citygeoid = @lv_city
          AND countygeoid = @lv_county.
        ENDIF.

        IF lv_regionid = 'ZCN-44-3' OR lv_regionid <> 'ZCN-44-16-9'."深圳\东莞连队
          IF lv_ordamt >= 50000."5w,不限车型
            lv_hit = 'X'.
            lv_parts_hit = 'X'.
            lv_risktypedesc = '安装风险'.
            lv_partsno = COND #( WHEN lv_partsno IS INITIAL THEN ls_item-partsnum ELSE |{ lv_partsno },{ ls_item-partsnum }| ).
            lv_price = COND #( WHEN lv_price IS INITIAL THEN lv_partsprice ELSE |{ lv_partsno },{ lv_partsprice }| ).
            lv_riskremark = '大额订单，奔宝奥路虎保时捷发动机，请及时提供技术支持'.
          ENDIF.
        ELSE."其他连队,2w,奔宝奥路保
          IF ( lv_carbrandid EQ 'BENZ' OR lv_carbrandid EQ 'BMW' OR lv_carbrandid EQ 'AUDI' OR lv_carbrandid EQ 'LANDROVER' OR lv_carbrandid EQ 'PORSCHE' ) AND lv_ordamt >= 20000.
            lv_hit = 'X'.
            lv_parts_hit = 'X'.
            lv_risktypedesc = '安装风险'.
            lv_partsno = COND #( WHEN lv_partsno IS INITIAL THEN ls_item-partsnum ELSE |{ lv_partsno },{ ls_item-partsnum }| ).
            lv_price = COND #( WHEN lv_price IS INITIAL THEN lv_partsprice ELSE |{ lv_partsno },{ lv_partsprice }| ).
            lv_riskremark = '大额订单，奔宝奥路虎保时捷发动机，请及时提供技术支持'.
          ENDIF.
        ENDIF.

      ELSEIF lv_partscategorycode EQ '10395'  OR lv_partscategorycode EQ '10396'.
        "该品类规则有预警过的不推
        READ TABLE lt_todoinf INTO ls_todoinf WITH KEY categoryid = lv_partscategorycode.
        IF sy-subrc EQ 0 AND ls_todoinf-acturalsellerprice >= 2000.
        ELSE.
          IF lv_partsprice >= 2000."金额超过2000的全部大灯.
            lv_hit = 'X'.
            lv_parts_hit = 'X'.
            lv_lighthit = 'X'.
            lv_risktypedesc = '安装风险、运输风险'.
            lv_partsno = COND #( WHEN lv_partsno IS INITIAL THEN ls_item-partsnum ELSE |{ lv_partsno },{ ls_item-partsnum }| ).
            lv_price = COND #( WHEN lv_price IS INITIAL THEN lv_partsprice ELSE |{ lv_partsno },{ lv_partsprice }| ).
            lv_riskremark = |安装的灯具，不可用干抹布擦拭表面，在用干布清洁的过程中，塑料防尘盖片会产生静电。从而吸附大灯中的微尘粒聚集在塑料防尘盖片内侧并形成冰花状的沉积物。| &&
            |绝对不要用干抹布或海绵清洁大灯/尾灯。另外，请不要使用含酒精的清洁材料，否则灯具表面有开裂风险。'|.


            SELECT * FROM ztint_visit_file APPENDING TABLE @DATA(lt_ztint_visit_filelight)
                WHERE guid = 'FA163EDBB6741FE085D4F60B29FA71B8'"写死
                AND isdelete = ''.

            SORT lt_ztint_visit_filelight BY zcrt_bdate zcrt_btime.

            IF lt_ztint_visit_filelight IS NOT INITIAL .
              SELECT * FROM ztint_wo_fl
              APPENDING TABLE @DATA(lt_ztint_wo_fllight)
                    FOR ALL ENTRIES IN @lt_ztint_visit_filelight
                    WHERE media_id = @lt_ztint_visit_filelight-mediaid
                    AND url <> ''.  "没有文件路径的图片不查出来
              SORT lt_ztint_wo_fllight BY media_id.
            ENDIF.
          ENDIF.
        ENDIF.

      ELSEIF lv_partscategorycode EQ '11020'  OR lv_partscategorycode EQ '11419'. "减震、减震芯
        IF lv_yxflag IS NOT INITIAL.
          "该品类规则有预警过的不推
          READ TABLE lt_todoinf INTO ls_todoinf WITH KEY categoryid = lv_partscategorycode.
          IF sy-subrc EQ 0 AND ls_todoinf-acturalsellerprice < 2000.
          ELSE.

            IF lv_partsprice < 2000."（预警对象是“机械普通减震”，按低价值触发）.
              lv_hit = 'X'.
              lv_parts_hit = 'X'.
              lv_lighthit = 'X'.
              lv_risktypedesc = '安装风险'.
              lv_partsno = COND #( WHEN lv_partsno IS INITIAL THEN ls_item-partsnum ELSE |{ lv_partsno },{ ls_item-partsnum }| ).
              lv_price = COND #( WHEN lv_price IS INITIAL THEN lv_partsprice ELSE |{ lv_partsno },{ lv_partsprice }| ).

              lv_riskremark = |更换麦弗逊式减震时，安装减震顶座螺丝时，请勿使用风炮进行拧紧，避免出现减震螺杆出现断裂，拧断后弹簧蹦出出现安全隐患。| &&
              |需要按照标准力矩进行拧紧减震螺丝。|.

            ENDIF.

          ENDIF.

        ENDIF.

      ELSE.

        "获取对应行项目的零件号的替换件
        oe_codes = ls_item-originalpartsnumtrim.
        REPLACE ALL OCCURRENCES OF  ',' IN oe_codes WITH ';'.
        CONDENSE oe_codes NO-GAPS. "去空格再调，确保LS_REPLACEDATA-param对应的是去空格的方便后面读表
        lo_mdm->get_oe_replacement_to( EXPORTING brand_code = lv_partsbrandid oe_codes = oe_codes IMPORTING es_oereplacement = DATA(ls_oereplacement) ).

        SPLIT ls_item-originalpartsnumtrim AT ',' INTO TABLE DATA(lt_partsnum).
        CLEAR: lv_isreplace.
        LOOP AT lt_partsnum INTO DATA(ls_partsnum).
          lv_partsnum = ls_partsnum."ls_item-originalpartsnumtrim.

          "先检查是否能命中配件规则
          READ TABLE lt_rule INTO DATA(ls_rule) WITH KEY type = 'Parts' partsnum = lv_partsnum carbrandid = lv_partsbrandid.
          IF sy-subrc NE 0."有维护指定配件
            CONDENSE lv_partsnum NO-GAPS.
            READ TABLE lt_rule INTO ls_rule WITH KEY type = 'Parts' partsnum = lv_partsnum carbrandid = lv_partsbrandid.
            IF sy-subrc NE 0."没找到。则找替换件是否能命中配件规则
              LOOP AT ls_oereplacement-data INTO DATA(ls_replacedata) WHERE param = lv_partsnum AND brand_code = lv_partsbrandid.

                LOOP AT ls_replacedata-replacement INTO DATA(ls_replacement).
                  READ TABLE ls_replacement-code_to INTO lv_codeto INDEX 1.
                  IF sy-subrc = 0.
                    READ TABLE lt_rule INTO ls_rule WITH KEY type = 'Parts' partsnum = lv_codeto carbrandid = ls_replacement-brand_code_to.
                    "替换号读到就退出循环，没读到就用去空格的再读
                    IF sy-subrc = 0.
                      lv_isreplace = 'X'."替换件命中
                      lv_replacenum = ls_rule-partsnum.
                      EXIT.
                    ELSE.
                      READ TABLE ls_replacement-code_trim_to INTO lv_codetrimto INDEX 1.
                      IF sy-subrc = 0.
                        READ TABLE lt_rule INTO ls_rule WITH KEY type = 'Parts' partsnum = lv_codetrimto carbrandid = ls_replacement-brand_code_to.
                        IF sy-subrc = 0.
                          lv_isreplace = 'X'."替换件命中
                          lv_replacenum = ls_rule-partsnum.
                          EXIT.
                        ENDIF.
                      ENDIF.
                    ENDIF.
                  ENDIF.

                ENDLOOP.

                "替换号读到就退出循环
                IF ls_rule IS NOT INITIAL.
                  EXIT.
                ENDIF.

              ENDLOOP.
            ENDIF.
          ENDIF.

          IF ls_rule-guid IS NOT INITIAL.
            "判定品质，城市范围是否符合
            LOOP AT lt_ruleext  INTO DATA(ls_ruleext) WHERE guid = ls_rule-guid AND zgroup NE 'TYPE'.
              IF lv_zoneexist IS NOT INITIAL AND lv_qualityexist IS NOT INITIAL.
                EXIT.
              ENDIF.

              IF ( ls_ruleext-zgroup = 'ZONE' AND lv_zoneexist IS NOT INITIAL )  "城市范围
              OR ( ls_ruleext-zgroup = 'QUALITY' AND lv_qualityexist IS NOT INITIAL )." 品质
                CONTINUE.
              ENDIF.

              IF ls_ruleext-zgroup = 'ZONE' AND lv_zoneexist IS INITIAL AND  "城市范围
              ( ls_ruleext-id EQ 'ALL' OR (
              ( ls_ruleext-provincegeoid  IS NOT INITIAL AND lv_prov EQ ls_ruleext-provincegeoid ) AND
              ( ls_ruleext-citygeoid IS INITIAL OR ( ls_ruleext-citygeoid IS NOT INITIAL AND lv_city EQ ls_ruleext-citygeoid ) ) ) ).
                lv_zoneexist = 'X'.
                CONTINUE.
              ENDIF.

              IF ls_ruleext-zgroup = 'QUALITY' AND lv_qualityexist IS INITIAL AND
              ( ( ls_ruleext-id EQ 'ALL' OR ls_ruleext-id EQ 'QUALITY_NO_LIMIT' ) OR
              ( ls_ruleext-id IS NOT INITIAL AND lv_partsquality EQ ls_ruleext-id ) ).
                lv_qualityexist = 'X'.
                CONTINUE.
              ENDIF.
              CLEAR ls_ruleext.
            ENDLOOP.

            IF lv_zoneexist IS NOT INITIAL AND lv_qualityexist IS NOT INITIAL.
              LOOP AT lt_ruleext INTO ls_ruleext WHERE guid = ls_rule-guid AND zgroup = 'TYPE'.
                APPEND ls_ruleext TO lt_risktype.
                CLEAR  ls_ruleext.
              ENDLOOP.

              SELECT SINGLE url INTO @lv_riskurl FROM ztint_visit_file AS v
              INNER JOIN ztint_wo_fl AS f ON v~mediaid = f~media_id
              WHERE v~guid = @ls_rule-guid AND isdelete = '' AND f~type = 'picture'.
              lt_parts = VALUE #( BASE lt_parts ( partsno = ls_item-partsnum partsname = ls_item-partsname
              partsprice = lv_partsprice
              partsurl = lv_riskurl  partsmark = ls_rule-remark ) ).

              lv_parts_hit = 'X'."命中品类规则
              lv_hit = 'X'.

              "配件对应附件获取
*   图片 文件
              SELECT * FROM ztint_visit_file INTO TABLE @DATA(lt_ztint_visit_file)
                    WHERE guid = @ls_rule-guid
                    AND isdelete = ''.

              SORT lt_ztint_visit_file BY zcrt_bdate zcrt_btime.

              IF  lt_ztint_visit_file IS NOT INITIAL .
                SELECT * FROM ztint_wo_fl
                APPENDING TABLE @DATA(lt_ztint_wo_fl)
                      FOR ALL ENTRIES IN @lt_ztint_visit_file
                      WHERE media_id = @lt_ztint_visit_file-mediaid
                      AND url <> ''.  "没有文件路径的图片不查出来
                SORT lt_ztint_wo_fl BY media_id.
              ENDIF.
              REFRESH lt_ztint_visit_file.

            ENDIF.

            CLEAR:lv_carexist,lv_zoneexist,lv_qualityexist,lv_riskurl.
          ENDIF.

          IF lv_hit IS NOT INITIAL.
            EXIT.
          ENDIF.

        ENDLOOP.

      ENDIF.
      CLEAR lt_partsnum.

      IF lv_hit IS INITIAL.
        "检查是否能命中品类规则
        READ TABLE lt_rule INTO ls_rule WITH KEY type = 'Class' classid = lv_partsmenucode4.
        IF sy-subrc NE 0 AND lv_partsmenucode3 IS NOT INITIAL.."有维护指定品类
          READ TABLE lt_rule INTO ls_rule WITH KEY type = 'Class' classid = lv_partsmenucode3.
          IF sy-subrc NE 0 AND lv_partsmenucode2 IS NOT INITIAL.
            READ TABLE lt_rule INTO ls_rule WITH KEY type = 'Class' classid = lv_partsmenucode2.
            IF sy-subrc NE 0 AND lv_partsmenucode1 IS NOT INITIAL.
              READ TABLE lt_rule INTO ls_rule WITH KEY type = 'Class' classid = lv_partsmenucode1.
            ENDIF.
          ENDIF.
        ENDIF.

        IF ls_rule-guid IS NOT INITIAL AND lv_partsprice > ls_rule-amount.
          "判定车辆品牌，城市范围，品质是否符合
          LOOP AT lt_ruleext INTO ls_ruleext WHERE guid = ls_rule-guid.
            IF lv_carexist IS NOT INITIAL AND lv_zoneexist IS NOT INITIAL
            AND lv_qualityexist IS NOT INITIAL.
              EXIT.
            ENDIF.

            IF ( ls_ruleext-zgroup = 'CAR' AND lv_carexist IS NOT INITIAL )
            OR ( ls_ruleext-zgroup = 'ZONE' AND lv_zoneexist IS NOT INITIAL )
            OR ( ls_ruleext-zgroup = 'QUALITY' AND lv_qualityexist IS NOT INITIAL ) .
              CONTINUE.
            ENDIF.

            IF ls_ruleext-zgroup = 'CAR' AND lv_carexist IS INITIAL AND
            ( ls_ruleext-id EQ 'ALL' OR ( ls_ruleext-id IS NOT INITIAL AND lv_carbrandid EQ ls_ruleext-id ) ).
              lv_carexist = 'X'.
            ENDIF.

            IF ls_ruleext-zgroup = 'ZONE' AND lv_zoneexist IS INITIAL AND  "城市范围
            ( ls_ruleext-id EQ 'ALL' OR (
            ( ls_ruleext-provincegeoid  IS NOT INITIAL AND lv_prov EQ ls_ruleext-provincegeoid ) AND
            ( ls_ruleext-citygeoid IS INITIAL OR ( ls_ruleext-citygeoid IS NOT INITIAL AND lv_city EQ ls_ruleext-citygeoid ) ) ) ).
              lv_zoneexist = 'X'.
            ENDIF.

            IF ls_ruleext-zgroup = 'QUALITY' AND lv_qualityexist IS INITIAL AND
            ( ls_ruleext-id EQ 'ALL' OR
            ( ls_ruleext-id IS NOT INITIAL AND lv_partsquality EQ ls_ruleext-id ) ).
              lv_qualityexist = 'X'.
            ENDIF.
            CLEAR ls_ruleext.
          ENDLOOP.

          IF lv_carexist IS NOT INITIAL AND lv_zoneexist IS NOT INITIAL
          AND lv_qualityexist IS NOT INITIAL.
            lv_class_hit = 'X'."命中品类规则
            lv_hit = 'X'.
          ENDIF.
          CLEAR:lv_carexist,lv_zoneexist,lv_qualityexist.
        ENDIF.
      ENDIF.

      IF lv_hit IS NOT INITIAL."有命中风险规则
        ls_hriskwo_i = VALUE #( orderid = ls_item-orderid orderitemseqid = ls_item-orderitemseqid
        brandid = ls_item-brandid brandname = ls_item-brandname
        productid = ls_item-productid productname = ls_item-productname
        categoryid = ls_item-categorycode categoryname = lv_partscategorycodenm
        quality = ls_item-quality qualityname = ls_item-qualityname
        acturalprice = ls_item-orderitempayment-buyeractualprice
        acturalsellerprice = ls_item-orderitempayment-sellerprice
        quantity = ls_item-quantity partsnum = ls_item-partsnum
        partsname = ls_item-productname waers =  'CNY'
        amount = ls_item-orderitempayment-buyeractualamount
        originalpartsnumtrim = ls_item-originalpartsnumtrim
        originalpartsnum = ls_item-originalpartsnum
        zcrt_uname =  sy-uname zcrt_bdate = sy-datum zcrt_btime = sy-uzeit ).
        SEARCH lv_partsname FOR ls_item-productname.
        IF sy-subrc NE 0.
          lv_partsname = COND #( WHEN lv_partsname IS INITIAL THEN ls_item-productname
          ELSE |{ lv_partsname }、{ ls_item-productname }| ).
        ENDIF.

        SEARCH lv_qualityname FOR ls_item-qualityname.
        IF sy-subrc NE 0.
          lv_qualityname = COND #( WHEN lv_qualityname IS INITIAL THEN ls_item-qualityname
          ELSE |{ lv_qualityname }、{ ls_item-qualityname }| ).
        ENDIF.
        APPEND ls_hriskwo_i TO lt_hriskwo_i.
      ENDIF.
      CLEAR:lv_partscategorycode,lv_partsnum,lv_partsbrandid,lv_partsquality,
      lv_hit,ls_rule,ls_ruleext,ls_hriskwo_i.
    ENDLOOP.

    "生成风险工单
    IF lv_parts_hit IS NOT INITIAL OR lv_class_hit IS NOT INITIAL."有命中
      DATA(lv_isneedspperson) = COND #( WHEN lv_parts_hit IS NOT INITIAL THEN 'X' ELSE '')."售后处理

      SELECT SINGLE cusid,cusname,companyid,companycode,servicelevel,provincegeoid,provincegeoname,
      citygeoid,citygeoname,countygeoid,countygeoname,villagegeoname,address INTO @DATA(ls_cus)
            FROM ztint_cus_inf WHERE companyid = @ls_detail-orderheader-buyer-companyid.
      "全程服务工单抬头信息
      ls_cus-servicelevel = COND #( WHEN ls_cus-servicelevel IS INITIAL THEN '1' ELSE ls_cus-servicelevel ).

      SELECT * INTO TABLE @DATA(lt_nodeinfo) FROM ztint_node_info WHERE node NE 'SHZ'.
      SORT lt_nodeinfo BY wotype servicelevel seq.
      READ TABLE lt_nodeinfo INTO DATA(ls_nextnode) WITH KEY wotype = 'FX' servicelevel = ls_cus-servicelevel
            seq = 2 BINARY SEARCH.

      lv_woid = create_hrisk_woid( EXPORTING wotype = 'FX'  CHANGING number = lv_number_pre ).

      ls_hriskwo_h = VALUE #( woid = lv_woid wotype = 'FX' node = COND #( WHEN lv_parts_hit IS NOT INITIAL THEN 'YWJ' ELSE ls_nextnode-node )
      orderid = ls_detail-orderheader-orderid
      companyid = ls_detail-orderheader-buyer-companyid companycode = ls_detail-orderheader-buyer-companycode
      cusid = ls_cus-cusid cusname = ls_detail-orderheader-buyer-companyname
      servicelevel = ls_cus-servicelevel
      productstoreid = ls_detail-orderheader-seller-companystoreid
      productstorename = ls_detail-orderheader-seller-companydisplayname
      carbrandid = lv_carbrandid carbrandname = lv_carbrandname
      followtype = COND #( WHEN lv_isneedspperson EQ 'X' THEN 'SP' ELSE  'CS' )
      status = COND #( WHEN lv_parts_hit IS NOT INITIAL THEN 'DONE'
      WHEN ls_nextnode-node EQ 'YWJ' THEN 'DONE'
      WHEN ls_nextnode-node EQ 'JSZC' AND ls_hriskwo_h-followtype EQ 'SP'
      THEN 'UNGET' ELSE 'DOING' )
      partsname = lv_partsname ruletype = COND #( WHEN lv_parts_hit IS NOT INITIAL THEN 'PARTS'
      ELSE 'CLASS' )
      zcrt_uname = sy-uname zcrt_bdate = sy-datum zcrt_btime = sy-uzeit
      zupd_uname = sy-uname zupd_bdate = sy-datum zupd_btime = sy-uzeit ).
      IF ls_detail-orderheader-orderdate IS NOT INITIAL.
        zcl_cassint_formatter=>convert_java_timestamp_to_abap(
        EXPORTING iv_timestamp = ls_detail-orderheader-orderdate
        IMPORTING ev_date =   ls_hriskwo_h-orderdate ev_time = ls_hriskwo_h-ordertime )."下单时间
      ENDIF.

      "新增工单新建节点
      TRY.
          ls_hriskwo_f = VALUE #( noteid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
          woid = lv_woid seq = 1 node = 'CREATE'
          nodedesc = '工单新建' status = 'DONE'
          zcrt_uname =  sy-uname zcrt_bdate = sy-datum zcrt_btime = sy-uzeit ).
          APPEND ls_hriskwo_f TO lt_hriskwo_f.
        CATCH cx_uuid_error.
      ENDTRY.

      "新增下一节点
      TRY.
          IF lv_class_hit IS NOT INITIAL.
            ls_nexthriskwo_f =  VALUE #( noteid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
            woid = lv_woid seq = 2 node = ls_nextnode-node
            nodedesc = ls_nextnode-nodedesc userid = sy-uname
            username = sy-uname zcrt_uname =  sy-uname zcrt_bdate = sy-datum zcrt_btime = sy-uzeit ).
            ls_nexthriskwo_f-operatordesc = COND #( WHEN  ls_nextnode-node EQ 'YWJ'  THEN '4级服务分级工单自动完结' ELSE  '' ).
            ls_nexthriskwo_f-status = COND #( WHEN ls_nextnode-node EQ 'YWJ' THEN 'DONE' ELSE 'DOING' ).

            "获取下一节点处理人
            IF ls_nextnode-node NE 'YWJ'.
              DATA(ls_nextuser) = get_nextnode_user_v2( is_address = ls_address ruletype = ls_hriskwo_h-ruletype
                    cusid = ls_cus-cusid wotype = ls_hriskwo_h-wotype  ).
              IF ls_nextuser-usertype = 'KF'.
                ls_hriskwo_h-csuserid = ls_nextuser-userid.
              ENDIF.
              ls_nexthriskwo_f-userid = ls_nextuser-userid .
              ls_nexthriskwo_f-username = ls_nextuser-username.
            ENDIF.

            APPEND ls_nexthriskwo_f TO lt_hriskwo_f.

            ls_hriskwo_h-followuserid = ls_nextuser-userid.
            ls_hriskwo_h-followusername = ls_nextuser-username.
          ELSEIF lv_parts_hit IS NOT INITIAL.
            "配件风险直接完成
            ls_nextuser = get_nextnode_user_v2( is_address = ls_address ruletype = ls_hriskwo_h-ruletype
            cusid = ls_cus-cusid wotype = ls_hriskwo_h-wotype  ).


            ls_nexthriskwo_f =  VALUE #( noteid = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( )
            woid = lv_woid seq = 2 node = 'YWJ'
            nodedesc = '已完结' status = 'DONE'
            userid = ls_nextuser-userid username = ls_nextuser-username
            operatordesc = '匹配配件风险件，已转售后技术处理'
            zcrt_uname =  sy-uname zcrt_bdate = sy-datum zcrt_btime = sy-uzeit ).

            APPEND ls_nexthriskwo_f TO lt_hriskwo_f.

            ls_hriskwo_h-followuserid = ls_nextuser-userid.
            ls_hriskwo_h-followusername = ls_nextuser-username.
          ENDIF.
        CATCH cx_uuid_error.
      ENDTRY.

      woid = ls_hriskwo_h-woid.

      IF ls_hriskwo_h IS NOT INITIAL.
        MODIFY ztint_hriskwo_h FROM ls_hriskwo_h.
      ENDIF.
      IF lt_hriskwo_f IS NOT INITIAL.
        MODIFY ztint_hriskwo_f FROM TABLE lt_hriskwo_f.
      ENDIF.
      IF lt_hriskwo_i IS NOT INITIAL.
        MODIFY ztint_hriskwo_i FROM TABLE lt_hriskwo_i.
      ENDIF.

      "风险服务工单推送
      "指定配件的推送给售后技术群，其他的推送给客服
      IF lv_parts_hit IS NOT INITIAL.
        "根据地址获取指定的微信群
        DATA:ls_sendmsg       TYPE zcl_corpweixin_api=>ts_msg,
             lv_urlstring     TYPE string,
             lv_todourlstring TYPE string,
             lv_msg_type      TYPE string,
             lt_msglist       TYPE STANDARD TABLE OF ztint_msg_list,
             lv_title         TYPE string..
        DATA:lv_cusname      TYPE string,
             lv_venname      TYPE string,
             lv_msgcontent   TYPE string,
             lt_user_content TYPE  zsint_push_userlist_tab,
             ls_user_content TYPE  zsint_push_userlist.

        DATA:lv_url1 TYPE string VALUE 'https://img1.baidu.com/it/u=2521516110,1741003725&fm=253&fmt=auto&app=138&f=JPEG?w=800&h=500',
             lv_url2 TYPE string VALUE 'https://img2.baidu.com/it/u=73055802,2758143707&fm=253&fmt=auto&app=138&f=JPEG?w=800&h=500',
             lv_url3 TYPE string VALUE 'https://img.mp.itc.cn/upload/20161030/0bf4745fadf046b3a68ece0763fc3010_th.jpeg'.
        DATA:lv_remark_cnt TYPE int4.
        DATA(lo_notice) = NEW zcl_cassint_notice( ).
        DATA(lv_key) = lo_notice->get_dept_robot_by_address(
              iv_busitype = 'HRISKWO_SH'
              is_address = ls_address ).

        IF lt_risktype IS NOT INITIAL.
          SORT lt_risktype BY zgroup id.
          DELETE ADJACENT DUPLICATES FROM lt_risktype COMPARING zgroup id.
          lv_risktypedesc = REDUCE #( INIT x TYPE string FOR wa IN lt_risktype
          NEXT x = COND #( WHEN x IS INITIAL THEN wa-vtext
          ELSE |{ x },{ wa-vtext }| ) ).
        ENDIF.

        SORT lt_parts BY partsno.
        DELETE ADJACENT DUPLICATES FROM lt_parts.
        LOOP AT lt_parts INTO DATA(ls_parts).
          IF ls_parts-partsno IS NOT INITIAL.
            lv_partsno =  COND #( WHEN lv_partsno IS INITIAL
            THEN |{ COND #( WHEN ls_parts-partsurl IS NOT INITIAL
            THEN |[{ ls_parts-partsno }]({ ls_parts-partsurl })|
            ELSE |{ ls_parts-partsno }| ) }|
            ELSE |{ lv_partsno }, { COND #( WHEN ls_parts-partsurl IS NOT INITIAL
            THEN |[{ ls_parts-partsno }]({ ls_parts-partsurl })|
            ELSE |[{ ls_parts-partsno }]| ) }|
            ).
            lv_price = COND #( WHEN lv_price IS INITIAL THEN ls_parts-partsprice
            ELSE |{ lv_price },{ ls_parts-partsprice }| ).
          ENDIF.
          IF ls_parts-partsmark IS NOT INITIAL.
            lv_remark_cnt = lv_remark_cnt + 1.
            lv_riskremark = COND #( WHEN lv_riskremark IS INITIAL THEN |{ lv_remark_cnt }.{  ls_parts-partsmark }|
            ELSE |{ lv_riskremark }; { lv_remark_cnt }.{  ls_parts-partsmark }| ).
          ENDIF.

        ENDLOOP.

        "消息ID
        DATA(lv_messageid) = cl_system_uuid=>if_system_uuid_static~create_uuid_c32( ).

        IF lv_key IS NOT INITIAL.
          "构建机器人消息
          SELECT SINGLE mobileurl FROM zticerp_ding_sk AS a INNER JOIN zticerp_ding_gid AS g
          ON a~gentid = g~gentid INTO @DATA(lv_hostname) WHERE g~fname = 'CASSINTQYWX'.
          lv_urlstring = |{ lv_hostname }#/order/{ ls_hriskwo_h-orderid }?isMessagePush=true|.
          lv_todourlstring = |{ lv_hostname }#/todoDetail/{ lv_messageid }?isMessagePush=true|.
          ls_sendmsg-msgtype = 'markdown'.

          ls_sendmsg-content = |<font COLOR=\\"warning\\">高风险件报备</font>  \\n  |." &&

          IF ls_hriskwo_h-followuserid IS NOT INITIAL.
            SELECT SINGLE qywxuserid INTO @DATA(lv_qwuserid) FROM ztint_user_inf WHERE userid = @ls_hriskwo_h-followuserid.
            IF sy-subrc EQ 0.
              ls_sendmsg-content = ls_sendmsg-content && |>售后人员：<@{ lv_qwuserid }> \\n |.
            ENDIF.
          ENDIF.
          "附件
          DATA lv_index TYPE i.
          "附件推送时按名称去重
          IF lv_lighthit IS NOT INITIAL.
            APPEND LINES OF lt_ztint_wo_fllight TO lt_ztint_wo_fl.
          ENDIF.
          SORT lt_ztint_wo_fl BY filename.
          DELETE ADJACENT DUPLICATES FROM lt_ztint_wo_fl COMPARING filename.
          LOOP AT  lt_ztint_wo_fl INTO DATA(ls_ztint_wo_fl).

            lv_index = lv_index + 1.
            IF lv_index = 1.
              ls_sendmsg-content = ls_sendmsg-content &&
              |>附件： \\n |.
            ENDIF.
            
            "判断是否为图片文件（通过文件扩展名）
            DATA: lv_lower_filename TYPE string,
                  lv_is_image       TYPE abap_bool VALUE abap_false.
            
            "转换为小写便于判断
            lv_lower_filename = ls_ztint_wo_fl-filename.
            TRANSLATE lv_lower_filename TO LOWER CASE.
            
            "判断常见图片格式
            IF lv_lower_filename CP '*.png' OR
               lv_lower_filename CP '*.jpg' OR
               lv_lower_filename CP '*.jpeg' OR
               lv_lower_filename CP '*.gif' OR
               lv_lower_filename CP '*.bmp' OR
               lv_lower_filename CP '*.webp'.
              lv_is_image = abap_true.
            ENDIF.
            
            IF lv_is_image = abap_true.
              "图片在同一个 Markdown 消息中显示
              "尝试标准 Markdown 图片语法（不使用引用块，直接嵌入）
              "注意：根据企业微信文档，Markdown 可能不支持图片直接渲染
              "如果此格式不生效，图片将显示为可点击的链接
              ls_sendmsg-content = ls_sendmsg-content &&
                |  \\n ![📷 { ls_ztint_wo_fl-filename }]({ ls_ztint_wo_fl-url }) \\n |.
            ELSE.
              "非图片文件：使用链接格式 [文件名](URL)
              ls_sendmsg-content = ls_sendmsg-content &&
                |>[{ ls_ztint_wo_fl-filename }]({ ls_ztint_wo_fl-url }) \\n |.
            ENDIF.

            CLEAR:ls_ztint_wo_fl.
          ENDLOOP.
          "替换件命中时加个标记（替换件）
          IF lv_isreplace = 'X' AND lv_replacenum IS NOT INITIAL.
            lv_partsno = |{ lv_partsno }(替换件: { lv_replacenum }）|.
          ENDIF.
          ls_sendmsg-content = ls_sendmsg-content &&
          |>订单：[{ ls_hriskwo_h-orderid }]({ lv_urlstring }) \\n | &&
          |>零件号：{ lv_partsno } \\n | &&
          |>零件名称：{ lv_partsname } \\n | &&
          |>零件价格：{ lv_price } \\n | &&
          |>车辆品牌：{ lv_carbrandname } \\n | &&
          |>品质：{ lv_qualityname } \\n | &&
          |>下单时间：{ ls_hriskwo_h-orderdate DATE = ISO } { ls_hriskwo_h-ordertime TIME = ISO } \\n | &&
          |>供应商：{ ls_detail-orderheader-fulfillstore-storename } \\n  | &&
          |>维修厂：{ ls_detail-orderheader-buyer-companydisplayname } | &&
          |{ COND #( WHEN lv_receive IS NOT INITIAL THEN |  \\n  >收货人姓名：{ lv_receive }| ) }| &&
          |{ COND #( WHEN lv_contactnumber IS NOT INITIAL THEN |  \\n  >手机号：{ lv_contactnumber }| ) }| &&
          |{ COND #( WHEN lv_risktypedesc IS NOT INITIAL THEN |  \\n  | &&
          |>风险类型：{ lv_risktypedesc }| ) }| &&
          |{ COND #( WHEN lv_riskremark IS NOT INITIAL THEN |  \\n  >风险提示：{ lv_riskremark }| ) }|.
          lv_msgcontent = |维修厂【{ ls_detail-orderheader-buyer-companydisplayname }({ ls_hriskwo_h-companycode })】订单{ ls_hriskwo_h-orderid }有高风险件；| &&
          |零件名称：{ lv_partsname }；| &&
          |车辆品牌：{ lv_carbrandname }；| &&
          |品质：{ lv_qualityname }；| &&
          |下单时间：{ ls_hriskwo_h-orderdate DATE = ISO } { ls_hriskwo_h-ordertime TIME = ISO }；| &&
          |供应商：{ ls_detail-orderheader-fulfillstore-storename }；| &&
          |{ COND #( WHEN lv_risktypedesc IS NOT INITIAL THEN || &&
          |风险类型：{ lv_risktypedesc }| ) }；| &&
          |{ COND #( WHEN lv_riskremark IS NOT INITIAL THEN |风险提示：{ lv_riskremark }| ) }；|.

          SELECT SINGLE i~userid,username INTO @DATA(ls_bd) FROM ztint_user_inf AS i
                INNER JOIN ztint_cus_user AS u ON i~userid = u~userid
                WHERE cusid = @ls_hriskwo_h-cusid AND i~isstill = 'X' AND u~usertype = '1' AND u~isdelete = '' AND u~ispre = ''.
          IF sy-subrc EQ 0.
            ls_sendmsg-content = ls_sendmsg-content &&
            |  \\n  >BD:{ ls_bd-username }|.
          ENDIF.
          IF lv_todourlstring IS NOT INITIAL.
            ls_sendmsg-content = ls_sendmsg-content &&
            |  \\n  >[立即处理]({ lv_todourlstring })|.
          ENDIF.
          lo_notice->set_robot_message(
          EXPORTING
            iv_key  = lv_key
            sendmsg = ls_sendmsg ).
        ENDIF.

        "进消息，转待办
        IF ls_hriskwo_h-followuserid IS NOT INITIAL.
          TRY.
              lt_msglist = VALUE #( BASE lt_msglist ( messageid = lv_messageid
              msgfirstcat = 'AS'
              msgsecondcat = 'NEWFXWO'
              touserid = ls_hriskwo_h-followuserid senddate = sy-datum sendtime = sy-uzeit msgstatus = '0'
              msgkeyvalue = ls_hriskwo_h-orderid
              msgkeyvalue_e = |{ ls_hriskwo_h-companycode },{ ls_hriskwo_h-productstoreid },{ ls_hriskwo_h-woid }|
              msgtitle = |订单【{ ls_hriskwo_h-orderid }】有高风险件，请及时跟进提醒|
              msgcontent = lv_msgcontent
              msgurl = ||
              appurlkey = |{ ls_hriskwo_h-orderid }|
              zcrt_bdate = sy-datum zcrt_btime = sy-uzeit zupd_bdate = sy-datum zupd_btime = sy-uzeit ) ).
            CATCH cx_uuid_error INTO DATA(lcx_nofound).
          ENDTRY.
          IF lt_msglist IS NOT INITIAL.
            CALL FUNCTION 'Z_FMINT_SAVE_MSGLIST'
              IN UPDATE TASK
              TABLES
                it_msglist = lt_msglist.
          ENDIF.
        ENDIF.
      ELSEIF lv_class_hit IS NOT INITIAL.
        DATA(lv_cusaddress) = |{ ls_cus-provincegeoname }{ ls_cus-citygeoname }{ ls_cus-countygeoname }| &&
              |{ ls_cus-villagegeoname }{ ls_cus-address }|.
        IF ls_nextnode NE 'YWJ' AND ls_nextuser-userid NE ''.
          "未处理每半小时后推送job
          CALL FUNCTION 'Z_FMINT_REMIND_FOLLOW_HRISKWO'
            EXPORTING
              iv_woid = ls_hriskwo_h-woid.

          lv_cusname = COND #( WHEN ls_detail-orderheader-buyer-companycode IS INITIAL THEN ls_detail-orderheader-buyer-companyname
          ELSE |{ ls_detail-orderheader-buyer-companyname }({ ls_detail-orderheader-buyer-companycode })| ).
          lv_venname = COND #( WHEN ls_detail-orderheader-seller-companystoreid IS INITIAL THEN ls_detail-orderheader-seller-companydisplayname
          ELSE |{ ls_detail-orderheader-seller-companydisplayname }({ ls_detail-orderheader-seller-companystoreid })| ).
          "添加推送消息
          ls_user_content-userid = ls_nextuser-userid.
          ls_user_content-orderno = ls_hriskwo_h-woid.
          ls_user_content-orderdesc = ls_hriskwo_h-node.
          ls_user_content-firstcatgory = '风险件工单'.
          ls_user_content-secondcatgory = '全程服务工单生成'.
          ls_user_content-wxcontent = |<div>【全程服务工单】{ ls_hriskwo_h-woid }需要你处理，请关注！ </div>| &&
          |<div CLASS=\\"highlight\\">当前节点：{ ls_nextnode-nodedesc }</div>| &&
          |<div>店铺名称：{ lv_venname }</div>| &&
          |<div>维修厂：{ lv_cusname }</div>| &&
          |<div>维修厂地址：{ lv_cusaddress }</div>| &&
          |<div>风险配件：{ lv_partsname }</div>| &&
          |<div>时间：{ sy-datum+0(4) }年{ sy-datum+4(2) }月{ sy-datum+6(2) }日 { sy-uzeit TIME = ISO }</div>| .
          APPEND ls_user_content TO lt_user_content.
          CLEAR:ls_user_content.

          IF lt_user_content IS NOT INITIAL.
            lv_title = '开思助手消息中心'.
            lv_msg_type = 'action_card'.
            lv_urlstring =  '#/newRiskSheet/detail/' && ls_hriskwo_h-woid  && '?isMessagePush=true' ."风险单详情
            CALL FUNCTION 'Z_FMINT_WO_PUSH'
              IN UPDATE TASK
              EXPORTING
                iv_title    = lv_title
                iv_msg_type = lv_msg_type
                iv_url      = lv_urlstring
              TABLES
                it_userlist = lt_user_content.
          ENDIF.
        ENDIF.
      ENDIF.
      COMMIT WORK AND WAIT.
    ENDIF.


  ENDMETHOD.