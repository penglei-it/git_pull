"============================================================================
"风险提醒条件配置表结构设计（九个筛选条件）
"============================================================================

"**************************************************************************
"1. 主表：风险提醒配置主表
"表名：ZTINT_RISK_FILTER_H
"功能：存储风险提醒规则的基本信息
"**************************************************************************

CREATE TABLE ztint_risk_filter_h (
  "主键
  guid              TYPE zguid PRIMARY KEY,              "规则唯一标识
  filter_code       TYPE string,                         "规则编码（唯一）
  filter_name       TYPE string,                         "规则名称
  risk_content      TYPE string,                         "风险提示文字内容
  risk_remark       TYPE string,                         "风险提示备注
  "状态相关
  isactive          TYPE char1,                         "是否启用：X=启用，空=停用
  isdelete          TYPE char1,                         "是否删除：X=已删除，空=未删除
  "时间戳
  zcrt_uname        TYPE string,                         "创建人
  zcrt_bdate        TYPE d,                              "创建日期
  zcrt_btime        TYPE t,                              "创建时间
  zupd_uname        TYPE string,                         "更新人
  zupd_bdate        TYPE d,                              "更新日期
  zupd_btime        TYPE t                               "更新时间
).

"**************************************************************************
"2. 条件明细表：存储九个筛选条件的配置值
"表名：ZTINT_RISK_FILTER_I
"功能：存储每个规则配置的筛选条件（支持多选）
"**************************************************************************

CREATE TABLE ztint_risk_filter_i (
  "主键
  guid              TYPE zguid,                          "关联主表 GUID
  seqno             TYPE int4,                           "序号（同一条件类型下的序号）
  condition_type    TYPE string,                         "条件类型（见下表）
  condition_value   TYPE string,                         "条件值
  condition_text    TYPE string,                         "条件显示文本（可选）
  "扩展字段（用于特殊条件）
  provincegeoid     TYPE string,                         "省份 ID（用于区域条件）
  citygeoid         TYPE string,                         "城市 ID（用于区域条件）
  countygeoid       TYPE string,                         "区县 ID（用于区域条件）
  regionid          TYPE string,                         "连队 ID（用于区域条件）
  price_min         TYPE p DECIMALS 2,                   "价格区间最小值（用于价格区间）
  price_max         TYPE p DECIMALS 2,                   "价格区间最大值（用于价格区间）
  "时间戳
  zcrt_uname        TYPE string,
  zcrt_bdate        TYPE d,
  zcrt_btime        TYPE t,
  PRIMARY KEY (guid, seqno, condition_type)
).

"条件类型（CONDITION_TYPE）取值说明：
"  COND1_CATEGORY     : 条件1 - 品类名称
"  COND2_PARTS_BRAND  : 条件2 - 零件品牌
"  COND3_PARTS_QUALITY: 条件3 - 零件品质
"  COND4_CAR_BRAND    : 条件4 - 汽车品牌
"  COND5_PRICE_RANGE  : 条件5 - 价格区间
"  COND6_REGION       : 条件6 - 区域（省市区或连队）
"  COND7_REPAIR_SHOP  : 条件7 - 维修厂名称
"  COND8_SUPPLIER     : 条件8 - 供应商名称
"  COND9_SPECIAL      : 条件9 - 是否严选件/中端车/高端车判断

"价格区间（CONDITION_VALUE）预设值：
"  RANGE_1_100        : [1,100]
"  RANGE_101_500      : [101,500]
"  RANGE_501_1000     : [501,1000]
"  RANGE_1001_2000    : [1001,2000]
"  RANGE_2001_3000    : [2001,3000]
"  RANGE_3000_PLUS    : 3000以上
"  或自定义区间（使用 price_min 和 price_max）

"条件9特殊值（CONDITION_VALUE）：
"  YANXUAN_YES        : 是严选件
"  YANXUAN_NO         : 非严选件
"  CAR_MID_RANGE      : 中端车
"  CAR_HIGH_RANGE     : 高端车

"**************************************************************************
"3. 附件表：存储风险提示的附件（图片/视频/PDF等）
"表名：ZTINT_RISK_FILTER_F
"功能：存储风险提示的附件信息
"**************************************************************************

CREATE TABLE ztint_risk_filter_f (
  guid              TYPE zguid,                          "关联主表 GUID
  mediaid           TYPE string,                         "媒体文件 ID（关联 ztint_wo_fl）
  file_type         TYPE string,                         "文件类型：picture/video/pdf/document
  filename           TYPE string,                         "文件名
  file_url          TYPE string,                         "文件 URL
  file_size         TYPE int8,                           "文件大小（字节）
  zcrt_uname        TYPE string,
  zcrt_bdate        TYPE d,
  zcrt_btime        TYPE t,
  isdelete          TYPE char1,
  PRIMARY KEY (guid, mediaid)
).

"注意：可以复用现有的 ztint_visit_file 和 ztint_wo_fl 表结构


"============================================================================
"匹配逻辑伪代码
"============================================================================

METHOD check_risk_filter_match.
  "**************************************************************************
  "功能：检查订单/询价单是否匹配风险提醒条件
  "输入参数：
  "  IS_ORDER_ITEM     : 订单行项目数据
  "  IS_ORDER_HEADER   : 订单抬头数据
  "  IS_CUSTOMER_INFO  : 客户信息（维修厂）
  "  IS_SUPPLIER_INFO  : 供应商信息
  "输出参数：
  "  ET_MATCHED_RULES  : 匹配的规则列表
  "  EV_MATCHED        : 是否匹配：X=匹配，空=不匹配
  "**************************************************************************

  "定义数据类型
  TYPES: BEGIN OF ty_risk_filter_h,
           guid         TYPE zguid,
           filter_code  TYPE string,
           filter_name  TYPE string,
           risk_content TYPE string,
           risk_remark  TYPE string,
         END OF ty_risk_filter_h.
  
  TYPES: BEGIN OF ty_risk_filter_i,
           guid           TYPE zguid,
           seqno          TYPE int4,
           condition_type TYPE string,
           condition_value TYPE string,
           condition_text TYPE string,
           provincegeoid  TYPE string,
           citygeoid      TYPE string,
           countygeoid    TYPE string,
           regionid       TYPE string,
           price_min      TYPE p DECIMALS 2,
           price_max      TYPE p DECIMALS 2,
         END OF ty_risk_filter_i.

  DATA: lt_filter_h      TYPE STANDARD TABLE OF ty_risk_filter_h,
        lt_filter_i       TYPE STANDARD TABLE OF ty_risk_filter_i,
        lt_matched_rules  TYPE STANDARD TABLE OF ty_risk_filter_h,
        ls_order_item     TYPE ty_order_item,
        ls_order_header   TYPE ty_order_header,
        ls_customer       TYPE ty_customer_info,
        ls_supplier       TYPE ty_supplier_info.

  "从输入参数获取数据（假设已传入）
  "ls_order_item = IS_ORDER_ITEM.
  "ls_order_header = IS_ORDER_HEADER.
  "ls_customer = IS_CUSTOMER_INFO.
  "ls_supplier = IS_SUPPLIER_INFO.

  "**************************************************************************
  "1. 加载所有启用的风险提醒规则
  "**************************************************************************
  SELECT * FROM ztint_risk_filter_h
    INTO TABLE lt_filter_h
    WHERE isactive = 'X'
      AND isdelete = ''.

  CHECK lt_filter_h IS NOT INITIAL.

  "加载所有规则的条件明细
  SELECT * FROM ztint_risk_filter_i
    INTO TABLE lt_filter_i
    FOR ALL ENTRIES IN lt_filter_h
    WHERE guid = lt_filter_h-guid.

  SORT lt_filter_i BY guid condition_type condition_value.

  "**************************************************************************
  "2. 提取订单/询价单的关键字段（用于匹配）
  "**************************************************************************
  DATA: lv_category_code   TYPE string,    "品类代码
        lv_category_name   TYPE string,    "品类名称
        lv_parts_brand_id  TYPE string,    "零件品牌 ID
        lv_parts_brand_name TYPE string,   "零件品牌名称
        lv_parts_quality   TYPE string,   "零件品质
        lv_car_brand_id    TYPE string,    "汽车品牌 ID
        lv_car_brand_name  TYPE string,    "汽车品牌名称
        lv_parts_price     TYPE p DECIMALS 2, "零件价格
        lv_province_geoid  TYPE string,    "省份 ID
        lv_city_geoid      TYPE string,    "城市 ID
        lv_county_geoid    TYPE string,    "区县 ID
        lv_region_id       TYPE string,    "连队 ID
        lv_repair_shop_id  TYPE string,    "维修厂 ID
        lv_repair_shop_name TYPE string,    "维修厂名称
        lv_supplier_id     TYPE string,    "供应商 ID
        lv_supplier_name   TYPE string,    "供应商名称
        lv_is_yanxuan      TYPE char1,     "是否严选件
        lv_car_type        TYPE string.    "车辆类型：MID/HIGH

  "从订单数据中提取字段
  lv_category_code = ls_order_item-categorycode.
  lv_category_name = ls_order_item-categoryname.
  lv_parts_brand_id = ls_order_item-brandid.
  lv_parts_brand_name = ls_order_item-brandname.
  lv_parts_quality = ls_order_item-quality.
  lv_car_brand_id = ls_order_header-carbrandid.
  lv_car_brand_name = ls_order_header-carbrandname.
  lv_parts_price = ls_order_item-orderitempayment-sellerprice.
  
  "地址信息
  lv_province_geoid = ls_order_header-orderpostaladdress-provincegeoid.
  lv_city_geoid = ls_order_header-orderpostaladdress-citygeoid.
  lv_county_geoid = ls_order_header-orderpostaladdress-countygeoid.
  
  "获取连队信息（根据省市区查询）
  SELECT SINGLE regionid INTO lv_region_id
    FROM ztint_area_city
    WHERE provincegeoid = lv_province_geoid
      AND citygeoid = lv_city_geoid
      AND countygeoid = lv_county_geoid.
  
  "维修厂信息
  lv_repair_shop_id = ls_order_header-buyer-companyid.
  lv_repair_shop_name = ls_order_header-buyer-companydisplayname.
  
  "供应商信息
  lv_supplier_id = ls_order_header-seller-companystoreid.
  lv_supplier_name = ls_order_header-seller-companydisplayname.
  
  "严选件判断（从订单抬头获取）
  lv_is_yanxuan = ls_order_header-ksbsorder.  "假设字段名
  
  "车辆类型判断（根据品牌判断中端/高端）
  "可以通过配置表或硬编码规则判断
  lv_car_type = get_car_type_by_brand( lv_car_brand_id ).

  "**************************************************************************
  "3. 遍历每个规则，检查是否匹配
  "**************************************************************************
  DATA: lv_match_count      TYPE int4,
        lv_condition_count  TYPE int4,
        lv_all_match        TYPE abap_bool VALUE abap_false.

  LOOP AT lt_filter_h INTO DATA(ls_filter_h).
    "初始化匹配状态
    CLEAR: lv_match_count, lv_condition_count, lv_all_match.
    
    "获取该规则的所有条件
    DATA(lt_rule_conditions) = FILTER #( lt_filter_i WHERE guid = ls_filter_h-guid ).
    
    "如果没有配置任何条件，则跳过（或者根据业务需求决定是否匹配）
    IF lt_rule_conditions IS INITIAL.
      CONTINUE.  "或 APPEND ls_filter_h TO lt_matched_rules（根据业务需求）
    ENDIF.

    "按条件类型分组统计
    DATA(lt_grouped_conditions) = VALUE string_table( ).
    SORT lt_rule_conditions BY condition_type.
    DELETE ADJACENT DUPLICATES FROM lt_rule_conditions COMPARING condition_type.
    
    "计算配置了哪些条件类型
    LOOP AT lt_rule_conditions INTO DATA(ls_cond_group).
      lv_condition_count = lv_condition_count + 1.
    ENDLOOP.

    "**************************************************************************
    "4. 逐个条件类型进行匹配（只要配置的条件都匹配即可）
    "   采用统一的匹配函数，减少代码重复
    "**************************************************************************
    
    "条件1：品类名称匹配
    DATA(lt_category_conds) = FILTER #( lt_rule_conditions WHERE condition_type = 'COND1_CATEGORY' ).
    IF lt_category_conds IS NOT INITIAL.
      IF check_condition_match( it_conditions = lt_category_conds
                                 iv_value1 = lv_category_code
                                 iv_value2 = lv_category_name ) = abap_false.
        CONTINUE.  "该条件不匹配，跳过当前规则
      ENDIF.
      lv_match_count = lv_match_count + 1.
    ENDIF.

    "条件2：零件品牌匹配
    DATA(lt_parts_brand_conds) = FILTER #( lt_rule_conditions WHERE condition_type = 'COND2_PARTS_BRAND' ).
    IF lt_parts_brand_conds IS NOT INITIAL.
      IF check_condition_match( it_conditions = lt_parts_brand_conds
                                 iv_value1 = lv_parts_brand_id
                                 iv_value2 = lv_parts_brand_name ) = abap_false.
        CONTINUE.
      ENDIF.
      lv_match_count = lv_match_count + 1.
    ENDIF.

    "条件3：零件品质匹配
    DATA(lt_quality_conds) = FILTER #( lt_rule_conditions WHERE condition_type = 'COND3_PARTS_QUALITY' ).
    IF lt_quality_conds IS NOT INITIAL.
      IF check_condition_match( it_conditions = lt_quality_conds
                                 iv_value1 = lv_parts_quality ) = abap_false.
        CONTINUE.
      ENDIF.
      lv_match_count = lv_match_count + 1.
    ENDIF.

    "条件4：汽车品牌匹配
    DATA(lt_car_brand_conds) = FILTER #( lt_rule_conditions WHERE condition_type = 'COND4_CAR_BRAND' ).
    IF lt_car_brand_conds IS NOT INITIAL.
      IF check_condition_match( it_conditions = lt_car_brand_conds
                                 iv_value1 = lv_car_brand_id
                                 iv_value2 = lv_car_brand_name ) = abap_false.
        CONTINUE.
      ENDIF.
      lv_match_count = lv_match_count + 1.
    ENDIF.

    "条件5：价格区间匹配
    DATA(lt_price_conds) = FILTER #( lt_rule_conditions WHERE condition_type = 'COND5_PRICE_RANGE' ).
    IF lt_price_conds IS NOT INITIAL.
      LOOP AT lt_price_conds INTO DATA(ls_price_cond).
        "处理预设区间
        CASE ls_price_cond-condition_value.
          WHEN 'RANGE_1_100'.
            IF lv_parts_price >= 1 AND lv_parts_price <= 100.
              lv_match_count = lv_match_count + 1.
              EXIT.
            ENDIF.
          WHEN 'RANGE_101_500'.
            IF lv_parts_price >= 101 AND lv_parts_price <= 500.
              lv_match_count = lv_match_count + 1.
              EXIT.
            ENDIF.
          WHEN 'RANGE_501_1000'.
            IF lv_parts_price >= 501 AND lv_parts_price <= 1000.
              lv_match_count = lv_match_count + 1.
              EXIT.
            ENDIF.
          WHEN 'RANGE_1001_2000'.
            IF lv_parts_price >= 1001 AND lv_parts_price <= 2000.
              lv_match_count = lv_match_count + 1.
              EXIT.
            ENDIF.
          WHEN 'RANGE_2001_3000'.
            IF lv_parts_price >= 2001 AND lv_parts_price <= 3000.
              lv_match_count = lv_match_count + 1.
              EXIT.
            ENDIF.
          WHEN 'RANGE_3000_PLUS'.
            IF lv_parts_price > 3000.
              lv_match_count = lv_match_count + 1.
              EXIT.
            ENDIF.
          WHEN OTHERS.
            "自定义区间（使用 price_min 和 price_max）
            IF ls_price_cond-price_min IS NOT INITIAL AND
               ls_price_cond-price_max IS NOT INITIAL.
              IF lv_parts_price >= ls_price_cond-price_min AND
                 lv_parts_price <= ls_price_cond-price_max.
                lv_match_count = lv_match_count + 1.
                EXIT.
              ENDIF.
            ENDIF.
        ENDCASE.
      ENDLOOP.
      IF sy-subrc NE 0.
        CONTINUE.
      ENDIF.
    ENDIF.

    "条件6：区域匹配（支持按连队或省市区）
    DATA(lt_region_conds) = FILTER #( lt_rule_conditions WHERE condition_type = 'COND6_REGION' ).
    IF lt_region_conds IS NOT INITIAL.
      LOOP AT lt_region_conds INTO DATA(ls_region_cond).
        "优先按连队匹配
        IF ls_region_cond-regionid IS NOT INITIAL.
          IF ls_region_cond-regionid = lv_region_id.
            lv_match_count = lv_match_count + 1.
            EXIT.
          ENDIF.
        "按省市区匹配
        ELSEIF ls_region_cond-provincegeoid IS NOT INITIAL.
          "省份必须匹配
          IF ls_region_cond-provincegeoid = lv_province_geoid.
            "城市可选，如果配置了城市，则必须匹配
            IF ls_region_cond-citygeoid IS INITIAL OR
               ls_region_cond-citygeoid = lv_city_geoid.
              "区县可选，如果配置了区县，则必须匹配
              IF ls_region_cond-countygeoid IS INITIAL OR
                 ls_region_cond-countygeoid = lv_county_geoid.
                lv_match_count = lv_match_count + 1.
                EXIT.
              ENDIF.
            ENDIF.
          ENDIF.
        ENDIF.
      ENDLOOP.
      IF sy-subrc NE 0.
        CONTINUE.
      ENDIF.
    ENDIF.

    "条件7：维修厂名称匹配
    DATA(lt_repair_conds) = FILTER #( lt_rule_conditions WHERE condition_type = 'COND7_REPAIR_SHOP' ).
    IF lt_repair_conds IS NOT INITIAL.
      IF check_condition_match( it_conditions = lt_repair_conds
                                 iv_value1 = lv_repair_shop_id
                                 iv_value2 = lv_repair_shop_name ) = abap_false.
        CONTINUE.
      ENDIF.
      lv_match_count = lv_match_count + 1.
    ENDIF.

    "条件8：供应商名称匹配
    DATA(lt_supplier_conds) = FILTER #( lt_rule_conditions WHERE condition_type = 'COND8_SUPPLIER' ).
    IF lt_supplier_conds IS NOT INITIAL.
      IF check_condition_match( it_conditions = lt_supplier_conds
                                 iv_value1 = lv_supplier_id
                                 iv_value2 = lv_supplier_name ) = abap_false.
        CONTINUE.
      ENDIF.
      lv_match_count = lv_match_count + 1.
    ENDIF.

    "条件9：是否严选件/中端车/高端车判断
    DATA(lt_special_conds) = FILTER #( lt_rule_conditions WHERE condition_type = 'COND9_SPECIAL' ).
    IF lt_special_conds IS NOT INITIAL.
      LOOP AT lt_special_conds INTO DATA(ls_special_cond).
        CASE ls_special_cond-condition_value.
          WHEN 'YANXUAN_YES'.
            IF lv_is_yanxuan = 'X'.
              lv_match_count = lv_match_count + 1.
              EXIT.
            ENDIF.
          WHEN 'YANXUAN_NO'.
            IF lv_is_yanxuan IS INITIAL OR lv_is_yanxuan = ''.
              lv_match_count = lv_match_count + 1.
              EXIT.
            ENDIF.
          WHEN 'CAR_MID_RANGE'.
            IF lv_car_type = 'MID'.
              lv_match_count = lv_match_count + 1.
              EXIT.
            ENDIF.
          WHEN 'CAR_HIGH_RANGE'.
            IF lv_car_type = 'HIGH'.
              lv_match_count = lv_match_count + 1.
              EXIT.
            ENDIF.
        ENDCASE.
      ENDLOOP.
      IF sy-subrc NE 0.
        CONTINUE.
      ENDIF.
    ENDIF.

    "**************************************************************************
    "5. 判断是否所有配置的条件都匹配
    "逻辑：只有当配置的条件数量 = 匹配的条件数量时，才认为完全匹配
    "**************************************************************************
    IF lv_condition_count > 0 AND lv_match_count = lv_condition_count.
      "所有配置的条件都匹配，添加到匹配结果
      APPEND ls_filter_h TO lt_matched_rules.
    ENDIF.

  ENDLOOP.

  "**************************************************************************
  "6. 返回匹配结果
  "**************************************************************************
  ET_MATCHED_RULES = lt_matched_rules.
  
  IF lt_matched_rules IS NOT INITIAL.
    EV_MATCHED = 'X'.
  ELSE.
    CLEAR EV_MATCHED.
  ENDIF.

ENDMETHOD.


"============================================================================
"通用匹配辅助方法（减少代码重复）
"============================================================================

METHOD check_condition_match.
  "**************************************************************************
  "功能：检查条件值是否匹配（通用方法，减少重复代码）
  "输入参数：
  "  IT_CONDITIONS  : 条件列表（内表）
  "  IV_VALUE1      : 主值（必填，如 ID）
  "  IV_VALUE2      : 辅助值（可选，如 Name，用于 ID 和 Name 双重匹配）
  "输出参数：
  "  RV_MATCHED     : 是否匹配（abap_true/abap_false）
  "**************************************************************************

  DATA: lt_conditions TYPE STANDARD TABLE OF ty_risk_filter_i,
        lv_value1     TYPE string,
        lv_value2     TYPE string,
        lv_matched    TYPE abap_bool VALUE abap_false.

  "参数赋值
  lt_conditions = it_conditions.
  lv_value1 = iv_value1.
  lv_value2 = iv_value2.

  "遍历条件列表，检查是否有匹配
  LOOP AT lt_conditions INTO DATA(ls_cond).
    "匹配主值或辅助值（任一匹配即可）
    IF ls_cond-condition_value = lv_value1 OR
       ( lv_value2 IS NOT INITIAL AND ls_cond-condition_value = lv_value2 ).
      lv_matched = abap_true.
      EXIT.  "找到匹配即退出
    ENDIF.
  ENDLOOP.

  RV_MATCHED = lv_matched.

ENDMETHOD.


"============================================================================
"辅助方法：根据品牌判断车辆类型
"============================================================================

METHOD get_car_type_by_brand.
  "输入：品牌 ID
  "输出：车辆类型（MID/HIGH/空）
  
  DATA: lv_brand_id TYPE string,
        lv_car_type TYPE string.

  lv_brand_id = iv_brand_id.

  "高端车品牌（示例）
  IF lv_brand_id = 'BENZ' OR
     lv_brand_id = 'BMW' OR
     lv_brand_id = 'AUDI' OR
     lv_brand_id = 'LANDROVER' OR
     lv_brand_id = 'PORSCHE' OR
     lv_brand_id = 'MERCEDES'.
    lv_car_type = 'HIGH'.
    "中端车品牌（可根据实际业务调整）
  ELSEIF lv_brand_id = 'TOYOTA' OR
         lv_brand_id = 'HONDA' OR
         lv_brand_id = 'NISSAN'.
    lv_car_type = 'MID'.
  ENDIF.

  RV_CAR_TYPE = lv_car_type.
  
ENDMETHOD.


"============================================================================
"优化建议和索引设计
"============================================================================

"1. 索引建议：
"   - ZTINT_RISK_FILTER_H: 在 (isactive, isdelete) 上创建索引
"   - ZTINT_RISK_FILTER_I: 在 (guid, condition_type) 上创建索引
"   - ZTINT_RISK_FILTER_I: 在 (condition_type, condition_value) 上创建索引（用于反向查询）

"2. 性能优化：
"   - 对于高频查询的条件类型（如品类、价格区间），考虑添加缓存
"   - 对于区域匹配，可以考虑建立区域映射表（省市区 -> 连队）
"   - 对于大量规则的场景，考虑按条件类型分组处理，减少循环次数

"3. 扩展性考虑：
"   - 如果未来需要支持更复杂的条件（如 AND/OR 组合），可以在主表添加逻辑表达式字段
"   - 如果条件值需要支持模糊匹配，可以在条件明细表添加匹配模式字段（EXACT/LIKE/REGEX）

"============================================================================
"数据示例
"============================================================================

"示例1：配置一个规则 - 高风险发动机提醒
"规则说明：当订单满足以下任一条件时触发提醒
"  - 品类：发动机（11366）或（10634）
"  - 价格：超过20000元
"  - 区域：深圳或东莞
"  - 严选件：是

"主表数据（ZTINT_RISK_FILTER_H）：
"GUID                              | FILTER_CODE     | FILTER_NAME          | RISK_CONTENT              | ISACTIVE
"----------------------------------|-----------------|----------------------|---------------------------|----------
"A1B2C3D4E5F6G7H8I9J0K1L2M3N4O5P6  | RISK_RULE_001   | 高风险发动机提醒      | 大额订单，请及时处理        | X

"明细表数据（ZTINT_RISK_FILTER_I）：
"GUID                              | SEQNO | CONDITION_TYPE     | CONDITION_VALUE | PRICE_MIN | PRICE_MAX
"----------------------------------|-------|--------------------|-----------------|-----------|----------
"A1B2C3D4E5F6G7H8I9J0K1L2M3N4O5P6  | 1     | COND1_CATEGORY      | 11366           |           |
"A1B2C3D4E5F6G7H8I9J0K1L2M3N4O5P6  | 2     | COND1_CATEGORY      | 10634           |           |
"A1B2C3D4E5F6G7H8I9J0K1L2M3N4O5P6  | 3     | COND5_PRICE_RANGE   | RANGE_2001_3000 | 2001      | 3000
"A1B2C3D4E5F6G7H8I9J0K1L2M3N4O5P6  | 4     | COND5_PRICE_RANGE   | RANGE_3000_PLUS | 3000      |
"A1B2C3D4E5F6G7H8I9J0K1L2M3N4O5P6  | 5     | COND6_REGION        |                 |           |
"A1B2C3D4E5F6G7H8I9J0K1L2M3N4O5P6  | 6     | COND9_SPECIAL       | YANXUAN_YES     |           |
"                                  |       |                    | REGIONID = ZCN-44-3 或 ZCN-44-16-9 |  |


"示例2：配置一个规则 - 大灯安装风险提醒
"规则说明：金额超过2000的大灯，提醒安装注意事项

"主表数据：
"GUID                              | FILTER_CODE     | FILTER_NAME          | RISK_CONTENT
"----------------------------------|-----------------|----------------------|---------------------------
"B2C3D4E5F6G7H8I9J0K1L2M3N4O5P6Q7  | RISK_RULE_002   | 大灯安装风险提醒      | 大灯不可用干抹布擦拭

"明细表数据：
"GUID                              | SEQNO | CONDITION_TYPE     | CONDITION_VALUE | PRICE_MIN | PRICE_MAX
"----------------------------------|-------|--------------------|-----------------|-----------|----------
"B2C3D4E5F6G7H8I9J0K1L2M3N4O5P6Q7  | 1     | COND1_CATEGORY      | 10395           |           |
"B2C3D4E5F6G7H8I9J0K1L2M3N4O5P6Q7  | 2     | COND1_CATEGORY      | 10396           |           |
"B2C3D4E5F6G7H8I9J0K1L2M3N4O5P6Q7  | 3     | COND5_PRICE_RANGE   | CUSTOM          | 2000      |


"============================================================================
"调用示例
"============================================================================

"在订单处理流程中调用匹配方法
DATA: ls_order_item   TYPE ty_order_item,
      ls_order_header TYPE ty_order_header,
      ls_customer     TYPE ty_customer_info,
      ls_supplier     TYPE ty_supplier_info,
      lt_matched      TYPE STANDARD TABLE OF ty_risk_filter_h,
      lv_matched      TYPE char1.

"获取订单数据（假设已从接口或数据库获取）
"ls_order_item = ...
"ls_order_header = ...
"ls_customer = ...
"ls_supplier = ...

"调用匹配方法
CALL METHOD check_risk_filter_match
  EXPORTING
    is_order_item    = ls_order_item
    is_order_header  = ls_order_header
    is_customer_info = ls_customer
    is_supplier_info = ls_supplier
  IMPORTING
    et_matched_rules = lt_matched
    ev_matched       = lv_matched.

"如果匹配到规则，发送风险提醒
IF lv_matched = 'X'.
  LOOP AT lt_matched INTO DATA(ls_rule).
    "获取该规则的附件
    SELECT * FROM ztint_risk_filter_f
      INTO TABLE @DATA(lt_attachments)
      WHERE guid = @ls_rule-guid
        AND isdelete = ''.

    "发送企业微信消息（使用之前创建的混合消息方法）
    "send_mixed_message_to_qywx(
    "  iv_group_key = '...'
    "  iv_text_title = ls_rule-filter_name
    "  iv_text_content = ls_rule-risk_content
    "  it_image_urls = ...
    "  it_doc_urls = ...
    ").
  ENDLOOP.
ENDIF.

