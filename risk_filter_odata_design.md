# 风险提示配置 OData 接口设计文档

## 一、业务需求概述

设计风险提示配置查询接口,支持以下9个筛选条件的多选、非必选、自由组合查询:

1. **品类 (CLASS)** - 支持四级分类
2. **零件品牌 (PARTSBRAND)**
3. **品质 (QUALITY)**
4. **汽车品牌 (CARBRAND)**
5. **价格区间 (PRICE)** - 离散区间: [1,100], [101,500], [501,1000], [1001,2000], [2001,3000], 3000以上
6. **连队 (REGION)**
7. **省市区 (ZONE)** - 省/市/区县/村镇
8. **维修厂/客户 (CUS)**
9. **供应商/商家 (STORE)**
10. **车辆等级 (CARGRADE)**

---

## 二、数据库表结构设计

### 2.1 主表: ZTINT_RISK_RULE (风险规则配置表)

```abap
@EndUserText.label : '风险提示规则配置表'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table ztint_risk_rule {
  
  key client      : abap.clnt not null;
  key rule_id     : abap.char(32) not null;      // 规则ID (GUID)
  
  // 基本信息
  rule_name       : abap.char(100);              // 规则名称
  rule_desc       : abap.string(0);              // 规则描述
  status          : abap.char(1);                // 状态: A-启用 D-停用
  priority        : abap.int4;                   // 优先级
  
  // 风险提示内容
  risk_title      : abap.char(100);              // 风险标题
  risk_content    : abap.string(0);              // 风险内容(文字描述)
  
  // 筛选条件 - 支持多选,用逗号分隔
  class_a         : abap.string(0);              // 一级分类(多选)
  class_b         : abap.string(0);              // 二级分类(多选)
  class_c         : abap.string(0);              // 三级分类(多选)
  class_d         : abap.string(0);              // 四级分类(多选)
  
  parts_brand     : abap.string(0);              // 零件品牌(多选)
  quality         : abap.string(0);              // 品质(多选)
  car_brand       : abap.string(0);              // 汽车品牌(多选)
  price_range     : abap.string(0);              // 价格区间(多选): 1-100,101-500,...
  
  region          : abap.string(0);              // 连队(多选)
  province_code   : abap.string(0);              // 省编码(多选)
  city_code       : abap.string(0);              // 市编码(多选)
  county_code     : abap.string(0);              // 区县编码(多选)
  village_code    : abap.string(0);              // 村镇编码(多选)
  
  customer_id     : abap.string(0);              // 维修厂/客户ID(多选)
  store_id        : abap.string(0);              // 供应商/店铺ID(多选)
  car_grade       : abap.string(0);              // 车辆等级(多选)
  is_strict       : abap.char(1);                // 是否严选件: X-是 空-否
  
  // 有效期
  valid_from      : abap.dats;                   // 生效日期
  valid_to        : abap.dats;                   // 失效日期
  
  // 审计字段
  zcrt_bdate      : abap.dats;                   // 创建日期
  zcrt_btime      : abap.tims;                   // 创建时间
  zcrt_uname      : abap.char(12);               // 创建人
  zupd_bdate      : abap.dats;                   // 修改日期
  zupd_btime      : abap.tims;                   // 修改时间
  zupd_uname      : abap.char(12);               // 修改人
  
}
```

### 2.2 附件表: ZTINT_RISK_FILE (风险提示附件表)

```abap
@EndUserText.label : '风险提示附件表'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
@AbapCatalog.tableCategory : #TRANSPARENT
define table ztint_risk_file {
  
  key client      : abap.clnt not null;
  key rule_id     : abap.char(32) not null;      // 规则ID
  key file_id     : abap.char(32) not null;      // 文件ID (GUID)
  
  file_name       : abap.char(255);              // 文件名
  file_type       : abap.char(10);               // 文件类型: IMG/VIDEO/PDF
  file_url        : abap.string(0);              // 文件URL
  file_size       : abap.int8;                   // 文件大小(字节)
  
  zcrt_bdate      : abap.dats;                   // 创建日期
  zcrt_btime      : abap.tims;                   // 创建时间
  zcrt_uname      : abap.char(12);               // 创建人
  
}
```

### 2.3 价格区间值域表: ZTINT_PRICE_RANGE (价格区间配置表)

```abap
@EndUserText.label : '价格区间配置表'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
@AbapCatalog.tableCategory : #TRANSPARENT
define table ztint_price_range {
  
  key client      : abap.clnt not null;
  key range_code  : abap.char(10) not null;      // 区间编码
  
  range_desc      : abap.char(50);               // 区间描述
  min_price       : abap.dec(15,2);              // 最小价格
  max_price       : abap.dec(15,2);              // 最大价格
  display_order   : abap.int2;                   // 显示顺序
  
}

// 初始数据
INSERT INTO ztint_price_range VALUES:
  ('RANGE01', '[1-100]',        1.00,    100.00,   10),
  ('RANGE02', '[101-500]',    101.00,    500.00,   20),
  ('RANGE03', '[501-1000]',   501.00,   1000.00,   30),
  ('RANGE04', '[1001-2000]', 1001.00,   2000.00,   40),
  ('RANGE05', '[2001-3000]', 2001.00,   3000.00,   50),
  ('RANGE06', '[3000以上]',  3000.00, 999999.99,   60).
```

---

## 三、CDS View 设计

### 3.1 主视图: ZI_RISK_RULE

```abap
@AbapCatalog.sqlViewName: 'ZVRISKRULE'
@AbapCatalog.compiler.compareFilter: true
@AbapCatalog.preserveKey: true
@AccessControl.authorizationCheck: #CHECK
@EndUserText.label: '风险提示规则视图'
@Metadata.allowExtensions: true
@ObjectModel.usageType:{
  serviceQuality: #X,
  sizeCategory: #S,
  dataClass: #MIXED
}

define view ZI_RISK_RULE
  as select from ztint_risk_rule as Rule
  
  // 关联附件表
  association [0..*] to ZI_RISK_FILE as _Files 
    on $projection.RuleId = _Files.RuleId
  
{
  key Rule.rule_id          as RuleId,
  
  // 基本信息
  Rule.rule_name            as RuleName,
  Rule.rule_desc            as RuleDesc,
  Rule.status               as Status,
  
  // 状态描述
  case Rule.status
    when 'A' then '启用'
    when 'D' then '停用'
    else ''
  end                       as StatusText,
  
  Rule.priority             as Priority,
  
  // 风险内容
  Rule.risk_title           as RiskTitle,
  Rule.risk_content         as RiskContent,
  
  // 筛选条件
  Rule.class_a              as ClassA,
  Rule.class_b              as ClassB,
  Rule.class_c              as ClassC,
  Rule.class_d              as ClassD,
  Rule.parts_brand          as PartsBrand,
  Rule.quality              as Quality,
  Rule.car_brand            as CarBrand,
  Rule.price_range          as PriceRange,
  Rule.region               as Region,
  Rule.province_code        as ProvinceCode,
  Rule.city_code            as CityCode,
  Rule.county_code          as CountyCode,
  Rule.village_code         as VillageCode,
  Rule.customer_id          as CustomerId,
  Rule.store_id             as StoreId,
  Rule.car_grade            as CarGrade,
  Rule.is_strict            as IsStrict,
  
  // 有效期
  Rule.valid_from           as ValidFrom,
  Rule.valid_to             as ValidTo,
  
  // 审计字段
  Rule.zcrt_bdate           as CreatedDate,
  Rule.zcrt_btime           as CreatedTime,
  Rule.zcrt_uname           as CreatedBy,
  Rule.zupd_bdate           as UpdatedDate,
  Rule.zupd_btime           as UpdatedTime,
  Rule.zupd_uname           as UpdatedBy,
  
  // 关联
  _Files
}
```

### 3.2 附件视图: ZI_RISK_FILE

```abap
@AbapCatalog.sqlViewName: 'ZVRISKFILE'
@AbapCatalog.compiler.compareFilter: true
@AbapCatalog.preserveKey: true
@AccessControl.authorizationCheck: #CHECK
@EndUserText.label: '风险提示附件视图'

define view ZI_RISK_FILE
  as select from ztint_risk_file
{
  key rule_id         as RuleId,
  key file_id         as FileId,
  
  file_name           as FileName,
  file_type           as FileType,
  
  // 文件类型描述
  case file_type
    when 'IMG'   then '图片'
    when 'VIDEO' then '视频'
    when 'PDF'   then 'PDF文档'
    else '其他'
  end                 as FileTypeText,
  
  file_url            as FileUrl,
  file_size           as FileSize,
  
  zcrt_bdate          as CreatedDate,
  zcrt_btime          as CreatedTime,
  zcrt_uname          as CreatedBy
}
```

---

## 四、OData Service 定义

### 4.1 Service Definition: ZSD_RISK_RULE

```abap
@EndUserText.label: '风险规则配置服务定义'
define service ZSD_RISK_RULE {
  expose ZI_RISK_RULE as RiskRule;
  expose ZI_RISK_FILE as RiskFile;
}
```

### 4.2 Service Binding: ZSB_RISK_RULE_O4

创建 OData V4 服务绑定:
- Service Definition: `ZSD_RISK_RULE`
- Binding Type: `OData V4 - UI`
- Service Name: `ZUI_RISK_RULE_O4`

---

## 五、OData 服务实现 (Redefinition)

### 5.1 创建类 ZCL_RISK_RULE_DPC_EXT

如果使用传统的Gateway方式,需要创建DPC扩展类:

```abap
CLASS zcl_risk_rule_dpc_ext DEFINITION
  PUBLIC
  INHERITING FROM zcl_risk_rule_dpc
  CREATE PUBLIC.

  PUBLIC SECTION.
    
    METHODS /iwbep/if_mgw_appl_srv_runtime~get_entityset
      REDEFINITION.
      
    METHODS /iwbep/if_mgw_appl_srv_runtime~get_entity
      REDEFINITION.

  PROTECTED SECTION.
    
    METHODS riskruleset_get_entityset
      IMPORTING
        iv_entity_name           TYPE string
        iv_entity_set_name       TYPE string
        iv_source_name           TYPE string
        it_filter_select_options TYPE /iwbep/t_mgw_select_option
        is_paging                TYPE /iwbep/s_mgw_paging
        it_key_tab               TYPE /iwbep/t_mgw_name_value_pair
        it_navigation_path       TYPE /iwbep/t_mgw_navigation_path
        it_order                 TYPE /iwbep/t_mgw_sorting_order
        iv_filter_string         TYPE string
        iv_search_string         TYPE string
        io_tech_request_context  TYPE REF TO /iwbep/if_mgw_req_entityset
      EXPORTING
        et_entityset             TYPE zttrisk_rule_tt
        es_response_context      TYPE /iwbep/if_mgw_appl_types=>ty_s_mgw_response_context
      RAISING
        /iwbep/cx_mgw_busi_exception
        /iwbep/cx_mgw_tech_exception.
        
    METHODS build_filter_range
      IMPORTING
        it_filter_select_options TYPE /iwbep/t_mgw_select_option
      EXPORTING
        er_class_a               TYPE RANGE OF ztint_risk_rule-class_a
        er_class_b               TYPE RANGE OF ztint_risk_rule-class_b
        er_class_c               TYPE RANGE OF ztint_risk_rule-class_c
        er_class_d               TYPE RANGE OF ztint_risk_rule-class_d
        er_parts_brand           TYPE RANGE OF ztint_risk_rule-parts_brand
        er_quality               TYPE RANGE OF ztint_risk_rule-quality
        er_car_brand             TYPE RANGE OF ztint_risk_rule-car_brand
        er_price_range           TYPE RANGE OF ztint_risk_rule-price_range
        er_region                TYPE RANGE OF ztint_risk_rule-region
        er_province              TYPE RANGE OF ztint_risk_rule-province_code
        er_city                  TYPE RANGE OF ztint_risk_rule-city_code
        er_county                TYPE RANGE OF ztint_risk_rule-county_code
        er_customer              TYPE RANGE OF ztint_risk_rule-customer_id
        er_store                 TYPE RANGE OF ztint_risk_rule-store_id
        er_car_grade             TYPE RANGE OF ztint_risk_rule-car_grade
        er_status                TYPE RANGE OF ztint_risk_rule-status.

ENDCLASS.

CLASS zcl_risk_rule_dpc_ext IMPLEMENTATION.

  METHOD /iwbep/if_mgw_appl_srv_runtime~get_entityset.
    
    CASE iv_entity_set_name.
      WHEN 'RiskRuleSet'.
        riskruleset_get_entityset(
          EXPORTING
            iv_entity_name           = iv_entity_name
            iv_entity_set_name       = iv_entity_set_name
            iv_source_name           = iv_source_name
            it_filter_select_options = it_filter_select_options
            is_paging                = is_paging
            it_key_tab               = it_key_tab
            it_navigation_path       = it_navigation_path
            it_order                 = it_order
            iv_filter_string         = iv_filter_string
            iv_search_string         = iv_search_string
            io_tech_request_context  = io_tech_request_context
          IMPORTING
            et_entityset             = er_entityset
            es_response_context      = es_response_context ).
            
      WHEN OTHERS.
        super->/iwbep/if_mgw_appl_srv_runtime~get_entityset(
          EXPORTING
            iv_entity_name           = iv_entity_name
            iv_entity_set_name       = iv_entity_set_name
            iv_source_name           = iv_source_name
            it_filter_select_options = it_filter_select_options
            is_paging                = is_paging
            it_key_tab               = it_key_tab
            it_navigation_path       = it_navigation_path
            it_order                 = it_order
            iv_filter_string         = iv_filter_string
            iv_search_string         = iv_search_string
            io_tech_request_context  = io_tech_request_context
          IMPORTING
            er_entityset             = er_entityset
            es_response_context      = es_response_context ).
    ENDCASE.
    
  ENDMETHOD.

  METHOD riskruleset_get_entityset.
    
    DATA: lt_rules       TYPE STANDARD TABLE OF ztint_risk_rule,
          lt_result      TYPE zttrisk_rule_tt,
          ls_result      LIKE LINE OF lt_result,
          lv_where_clause TYPE string,
          lt_where_parts  TYPE STANDARD TABLE OF string.
    
    " 构建筛选Range
    DATA: lr_class_a     TYPE RANGE OF ztint_risk_rule-class_a,
          lr_class_b     TYPE RANGE OF ztint_risk_rule-class_b,
          lr_class_c     TYPE RANGE OF ztint_risk_rule-class_c,
          lr_class_d     TYPE RANGE OF ztint_risk_rule-class_d,
          lr_parts_brand TYPE RANGE OF ztint_risk_rule-parts_brand,
          lr_quality     TYPE RANGE OF ztint_risk_rule-quality,
          lr_car_brand   TYPE RANGE OF ztint_risk_rule-car_brand,
          lr_price_range TYPE RANGE OF ztint_risk_rule-price_range,
          lr_region      TYPE RANGE OF ztint_risk_rule-region,
          lr_province    TYPE RANGE OF ztint_risk_rule-province_code,
          lr_city        TYPE RANGE OF ztint_risk_rule-city_code,
          lr_county      TYPE RANGE OF ztint_risk_rule-county_code,
          lr_customer    TYPE RANGE OF ztint_risk_rule-customer_id,
          lr_store       TYPE RANGE OF ztint_risk_rule-store_id,
          lr_car_grade   TYPE RANGE OF ztint_risk_rule-car_grade,
          lr_status      TYPE RANGE OF ztint_risk_rule-status.
    
    " 解析过滤条件
    build_filter_range(
      EXPORTING
        it_filter_select_options = it_filter_select_options
      IMPORTING
        er_class_a               = lr_class_a
        er_class_b               = lr_class_b
        er_class_c               = lr_class_c
        er_class_d               = lr_class_d
        er_parts_brand           = lr_parts_brand
        er_quality               = lr_quality
        er_car_brand             = lr_car_brand
        er_price_range           = lr_price_range
        er_region                = lr_region
        er_province              = lr_province
        er_city                  = lr_city
        er_county                = lr_county
        er_customer              = lr_customer
        er_store                 = lr_store
        er_car_grade             = lr_car_grade
        er_status                = lr_status ).
    
    " 基础查询
    SELECT * FROM ztint_risk_rule
      INTO TABLE @lt_rules
      WHERE status IN @lr_status
        AND ( valid_from IS NULL OR valid_from <= @sy-datum )
        AND ( valid_to IS NULL OR valid_to >= @sy-datum ).
    
    " 应用业务过滤逻辑 - 支持多选条件的CONTAINS判断
    LOOP AT lt_rules INTO DATA(ls_rule).
      
      DATA(lv_match) = abap_true.
      
      " 品类筛选 - 支持多级品类
      IF lr_class_a IS NOT INITIAL.
        CHECK zcl_risk_rule_helper=>check_multi_value_match(
          iv_stored_value = ls_rule-class_a
          it_filter_range = lr_class_a ) = abap_true.
      ENDIF.
      
      IF lr_class_b IS NOT INITIAL.
        CHECK zcl_risk_rule_helper=>check_multi_value_match(
          iv_stored_value = ls_rule-class_b
          it_filter_range = lr_class_b ) = abap_true.
      ENDIF.
      
      IF lr_class_c IS NOT INITIAL.
        CHECK zcl_risk_rule_helper=>check_multi_value_match(
          iv_stored_value = ls_rule-class_c
          it_filter_range = lr_class_c ) = abap_true.
      ENDIF.
      
      IF lr_class_d IS NOT INITIAL.
        CHECK zcl_risk_rule_helper=>check_multi_value_match(
          iv_stored_value = ls_rule-class_d
          it_filter_range = lr_class_d ) = abap_true.
      ENDIF.
      
      " 零件品牌筛选
      IF lr_parts_brand IS NOT INITIAL.
        CHECK zcl_risk_rule_helper=>check_multi_value_match(
          iv_stored_value = ls_rule-parts_brand
          it_filter_range = lr_parts_brand ) = abap_true.
      ENDIF.
      
      " 品质筛选
      IF lr_quality IS NOT INITIAL.
        CHECK zcl_risk_rule_helper=>check_multi_value_match(
          iv_stored_value = ls_rule-quality
          it_filter_range = lr_quality ) = abap_true.
      ENDIF.
      
      " 汽车品牌筛选
      IF lr_car_brand IS NOT INITIAL.
        CHECK zcl_risk_rule_helper=>check_multi_value_match(
          iv_stored_value = ls_rule-car_brand
          it_filter_range = lr_car_brand ) = abap_true.
      ENDIF.
      
      " 价格区间筛选
      IF lr_price_range IS NOT INITIAL.
        CHECK zcl_risk_rule_helper=>check_multi_value_match(
          iv_stored_value = ls_rule-price_range
          it_filter_range = lr_price_range ) = abap_true.
      ENDIF.
      
      " 连队筛选
      IF lr_region IS NOT INITIAL.
        CHECK zcl_risk_rule_helper=>check_multi_value_match(
          iv_stored_value = ls_rule-region
          it_filter_range = lr_region ) = abap_true.
      ENDIF.
      
      " 省市区筛选
      IF lr_province IS NOT INITIAL.
        CHECK zcl_risk_rule_helper=>check_multi_value_match(
          iv_stored_value = ls_rule-province_code
          it_filter_range = lr_province ) = abap_true.
      ENDIF.
      
      IF lr_city IS NOT INITIAL.
        CHECK zcl_risk_rule_helper=>check_multi_value_match(
          iv_stored_value = ls_rule-city_code
          it_filter_range = lr_city ) = abap_true.
      ENDIF.
      
      IF lr_county IS NOT INITIAL.
        CHECK zcl_risk_rule_helper=>check_multi_value_match(
          iv_stored_value = ls_rule-county_code
          it_filter_range = lr_county ) = abap_true.
      ENDIF.
      
      " 客户筛选
      IF lr_customer IS NOT INITIAL.
        CHECK zcl_risk_rule_helper=>check_multi_value_match(
          iv_stored_value = ls_rule-customer_id
          it_filter_range = lr_customer ) = abap_true.
      ENDIF.
      
      " 供应商筛选
      IF lr_store IS NOT INITIAL.
        CHECK zcl_risk_rule_helper=>check_multi_value_match(
          iv_stored_value = ls_rule-store_id
          it_filter_range = lr_store ) = abap_true.
      ENDIF.
      
      " 车辆等级筛选
      IF lr_car_grade IS NOT INITIAL.
        CHECK zcl_risk_rule_helper=>check_multi_value_match(
          iv_stored_value = ls_rule-car_grade
          it_filter_range = lr_car_grade ) = abap_true.
      ENDIF.
      
      " 匹配成功,加入结果集
      MOVE-CORRESPONDING ls_rule TO ls_result.
      APPEND ls_result TO lt_result.
      CLEAR: ls_result, lv_match.
      
    ENDLOOP.
    
    " 排序处理
    IF it_order IS NOT INITIAL.
      " 根据排序要求进行排序
      SORT lt_result BY priority DESCENDING.
    ENDIF.
    
    " 分页处理
    IF is_paging-top IS NOT INITIAL.
      DELETE lt_result FROM ( is_paging-skip + is_paging-top + 1 ).
    ENDIF.
    
    IF is_paging-skip IS NOT INITIAL.
      DELETE lt_result TO is_paging-skip.
    ENDIF.
    
    et_entityset = lt_result.
    
  ENDMETHOD.

  METHOD build_filter_range.
    
    " 遍历所有过滤条件
    LOOP AT it_filter_select_options INTO DATA(ls_filter).
      
      CASE ls_filter-property.
        WHEN 'CLASS_A' OR 'CLASSA'.
          er_class_a = ls_filter-select_options.
          
        WHEN 'CLASS_B' OR 'CLASSB'.
          er_class_b = ls_filter-select_options.
          
        WHEN 'CLASS_C' OR 'CLASSC'.
          er_class_c = ls_filter-select_options.
          
        WHEN 'CLASS_D' OR 'CLASSD'.
          er_class_d = ls_filter-select_options.
          
        WHEN 'PARTS_BRAND' OR 'PARTSBRAND'.
          er_parts_brand = ls_filter-select_options.
          
        WHEN 'QUALITY'.
          er_quality = ls_filter-select_options.
          
        WHEN 'CAR_BRAND' OR 'CARBRAND'.
          er_car_brand = ls_filter-select_options.
          
        WHEN 'PRICE_RANGE' OR 'PRICERANGE'.
          er_price_range = ls_filter-select_options.
          
        WHEN 'REGION'.
          er_region = ls_filter-select_options.
          
        WHEN 'PROVINCE_CODE' OR 'PROVINCE'.
          er_province = ls_filter-select_options.
          
        WHEN 'CITY_CODE' OR 'CITY'.
          er_city = ls_filter-select_options.
          
        WHEN 'COUNTY_CODE' OR 'COUNTY'.
          er_county = ls_filter-select_options.
          
        WHEN 'CUSTOMER_ID' OR 'CUSTOMER'.
          er_customer = ls_filter-select_options.
          
        WHEN 'STORE_ID' OR 'STORE'.
          er_store = ls_filter-select_options.
          
        WHEN 'CAR_GRADE' OR 'CARGRADE'.
          er_car_grade = ls_filter-select_options.
          
        WHEN 'STATUS'.
          er_status = ls_filter-select_options.
          
      ENDCASE.
      
    ENDLOOP.
    
  ENDMETHOD.

ENDCLASS.
```

---

## 六、辅助工具类 ZCL_RISK_RULE_HELPER

```abap
CLASS zcl_risk_rule_helper DEFINITION
  PUBLIC
  FINAL
  CREATE PUBLIC.

  PUBLIC SECTION.
    
    CLASS-METHODS check_multi_value_match
      IMPORTING
        iv_stored_value TYPE string
        it_filter_range TYPE ANY TABLE
      RETURNING
        VALUE(rv_match) TYPE abap_bool.
        
    CLASS-METHODS split_multi_values
      IMPORTING
        iv_value_string     TYPE string
      RETURNING
        VALUE(rt_values)    TYPE string_table.

ENDCLASS.

CLASS zcl_risk_rule_helper IMPLEMENTATION.

  METHOD check_multi_value_match.
    
    " 如果存储值为空,表示不限制,匹配所有
    IF iv_stored_value IS INITIAL.
      rv_match = abap_true.
      RETURN.
    ENDIF.
    
    " 分割存储的多值 (格式: "VALUE1,VALUE2,VALUE3")
    DATA(lt_stored_values) = split_multi_values( iv_stored_value ).
    
    " 遍历过滤条件
    LOOP AT it_filter_range ASSIGNING FIELD-SYMBOL(<fs_range>).
      
      DATA(lv_filter_value) = CONV string( <fs_range>-('LOW') ).
      
      " 检查存储值中是否包含该过滤值
      READ TABLE lt_stored_values TRANSPORTING NO FIELDS
        WITH KEY table_line = lv_filter_value.
        
      IF sy-subrc = 0.
        rv_match = abap_true.
        RETURN.
      ENDIF.
      
    ENDLOOP.
    
    " 如果没有匹配项
    rv_match = abap_false.
    
  ENDMETHOD.

  METHOD split_multi_values.
    
    " 按逗号分割字符串
    SPLIT iv_value_string AT ',' INTO TABLE rt_values.
    
    " 去除空格
    LOOP AT rt_values ASSIGNING FIELD-SYMBOL(<fs_value>).
      <fs_value> = condense( <fs_value> ).
    ENDLOOP.
    
    " 删除空值
    DELETE rt_values WHERE table_line IS INITIAL.
    
  ENDMETHOD.

ENDCLASS.
```

---

## 七、OData 查询示例

### 7.1 基础查询 - 获取所有启用的规则

```http
GET /sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet?$filter=Status eq 'A'
```

### 7.2 单一条件查询 - 按品类查询

```http
GET /sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet?$filter=ClassA eq 'CAT001'
```

### 7.3 多条件组合查询 - 品类 + 品牌 + 价格区间

```http
GET /sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet
  ?$filter=ClassA eq 'CAT001' 
    and PartsBrand eq 'BRAND001' 
    and PriceRange eq 'RANGE02'
```

### 7.4 多选查询 - IN 操作符

```http
GET /sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet
  ?$filter=ClassA in ('CAT001','CAT002','CAT003')
    and CarBrand in ('BMW','BENZ','AUDI')
```

### 7.5 区域组合查询 - 省市区联动

```http
GET /sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet
  ?$filter=ProvinceCode eq '110000' 
    and CityCode eq '110100'
    and CountyCode eq '110101'
```

### 7.6 包含附件的完整查询

```http
GET /sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet
  ?$filter=Status eq 'A'
  &$expand=_Files
  &$orderby=Priority desc
  &$top=20
```

### 7.7 复杂组合查询示例

```http
GET /sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet
  ?$filter=(ClassA eq 'CAT001' or ClassA eq 'CAT002')
    and Quality eq 'OEM'
    and PriceRange in ('RANGE04','RANGE05')
    and ProvinceCode eq '110000'
    and Status eq 'A'
  &$expand=_Files
  &$orderby=Priority desc,CreatedDate desc
  &$top=50
  &$skip=0
```

---

## 八、调用示例 (ABAP 代码)

### 8.1 从订单/询价单匹配风险规则

```abap
CLASS zcl_risk_matcher DEFINITION
  PUBLIC
  FINAL
  CREATE PUBLIC.

  PUBLIC SECTION.
    
    TYPES: BEGIN OF ty_inquiry_data,
             inquiry_id   TYPE zde_inquiry_id,
             class_a      TYPE zde_class_a,
             class_b      TYPE zde_class_b,
             parts_brand  TYPE zde_brand_id,
             quality      TYPE zde_quality,
             car_brand    TYPE zde_car_brand,
             price        TYPE zde_price,
             region       TYPE zde_region,
             province     TYPE zde_province,
             city         TYPE zde_city,
             county       TYPE zde_county,
             customer_id  TYPE zde_customer_id,
             store_id     TYPE zde_store_id,
             car_grade    TYPE zde_car_grade,
           END OF ty_inquiry_data.
    
    TYPES: BEGIN OF ty_risk_result,
             rule_id      TYPE ztint_risk_rule-rule_id,
             rule_name    TYPE ztint_risk_rule-rule_name,
             risk_title   TYPE ztint_risk_rule-risk_title,
             risk_content TYPE ztint_risk_rule-risk_content,
             priority     TYPE ztint_risk_rule-priority,
           END OF ty_risk_result.
    
    TYPES: tt_risk_result TYPE STANDARD TABLE OF ty_risk_result WITH KEY rule_id.
    
    CLASS-METHODS match_risk_rules
      IMPORTING
        is_inquiry_data  TYPE ty_inquiry_data
      EXPORTING
        et_matched_rules TYPE tt_risk_result
        ev_has_risk      TYPE abap_bool.

ENDCLASS.

CLASS zcl_risk_matcher IMPLEMENTATION.

  METHOD match_risk_rules.
    
    DATA: lt_rules TYPE STANDARD TABLE OF ztint_risk_rule,
          ls_result TYPE ty_risk_result.
    
    " 获取所有启用的风险规则
    SELECT * FROM ztint_risk_rule
      INTO TABLE @lt_rules
      WHERE status = 'A'
        AND ( valid_from IS INITIAL OR valid_from <= @sy-datum )
        AND ( valid_to IS INITIAL OR valid_to >= @sy-datum )
      ORDER BY priority DESCENDING.
    
    " 逐条匹配规则
    LOOP AT lt_rules INTO DATA(ls_rule).
      
      DATA(lv_match) = abap_true.
      
      " 检查品类 - 只要有一个匹配就符合
      IF ls_rule-class_a IS NOT INITIAL.
        IF NOT zcl_risk_rule_helper=>check_value_in_list(
          iv_value = is_inquiry_data-class_a
          iv_list = ls_rule-class_a ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      IF ls_rule-class_b IS NOT INITIAL.
        IF NOT zcl_risk_rule_helper=>check_value_in_list(
          iv_value = is_inquiry_data-class_b
          iv_list = ls_rule-class_b ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      " 检查零件品牌
      IF ls_rule-parts_brand IS NOT INITIAL.
        IF NOT zcl_risk_rule_helper=>check_value_in_list(
          iv_value = is_inquiry_data-parts_brand
          iv_list = ls_rule-parts_brand ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      " 检查品质
      IF ls_rule-quality IS NOT INITIAL.
        IF NOT zcl_risk_rule_helper=>check_value_in_list(
          iv_value = is_inquiry_data-quality
          iv_list = ls_rule-quality ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      " 检查汽车品牌
      IF ls_rule-car_brand IS NOT INITIAL.
        IF NOT zcl_risk_rule_helper=>check_value_in_list(
          iv_value = is_inquiry_data-car_brand
          iv_list = ls_rule-car_brand ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      " 检查价格区间
      IF ls_rule-price_range IS NOT INITIAL.
        DATA(lv_price_range) = get_price_range_code( is_inquiry_data-price ).
        IF NOT zcl_risk_rule_helper=>check_value_in_list(
          iv_value = lv_price_range
          iv_list = ls_rule-price_range ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      " 检查连队
      IF ls_rule-region IS NOT INITIAL.
        IF NOT zcl_risk_rule_helper=>check_value_in_list(
          iv_value = is_inquiry_data-region
          iv_list = ls_rule-region ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      " 检查省市区
      IF ls_rule-province_code IS NOT INITIAL.
        IF NOT zcl_risk_rule_helper=>check_value_in_list(
          iv_value = is_inquiry_data-province
          iv_list = ls_rule-province_code ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      IF ls_rule-city_code IS NOT INITIAL.
        IF NOT zcl_risk_rule_helper=>check_value_in_list(
          iv_value = is_inquiry_data-city
          iv_list = ls_rule-city_code ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      IF ls_rule-county_code IS NOT INITIAL.
        IF NOT zcl_risk_rule_helper=>check_value_in_list(
          iv_value = is_inquiry_data-county
          iv_list = ls_rule-county_code ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      " 检查客户
      IF ls_rule-customer_id IS NOT INITIAL.
        IF NOT zcl_risk_rule_helper=>check_value_in_list(
          iv_value = is_inquiry_data-customer_id
          iv_list = ls_rule-customer_id ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      " 检查供应商
      IF ls_rule-store_id IS NOT INITIAL.
        IF NOT zcl_risk_rule_helper=>check_value_in_list(
          iv_value = is_inquiry_data-store_id
          iv_list = ls_rule-store_id ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      " 检查车辆等级
      IF ls_rule-car_grade IS NOT INITIAL.
        IF NOT zcl_risk_rule_helper=>check_value_in_list(
          iv_value = is_inquiry_data-car_grade
          iv_list = ls_rule-car_grade ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      " 所有条件都匹配,加入结果
      ls_result = VALUE #(
        rule_id      = ls_rule-rule_id
        rule_name    = ls_rule-rule_name
        risk_title   = ls_rule-risk_title
        risk_content = ls_rule-risk_content
        priority     = ls_rule-priority ).
      
      APPEND ls_result TO et_matched_rules.
      CLEAR ls_result.
      
    ENDLOOP.
    
    " 判断是否有风险
    IF et_matched_rules IS NOT INITIAL.
      ev_has_risk = abap_true.
    ELSE.
      ev_has_risk = abap_false.
    ENDIF.
    
  ENDMETHOD.
  
  METHOD get_price_range_code.
    " 根据价格返回对应的区间编码
    " 实现逻辑...
  ENDMETHOD.

ENDCLASS.
```

### 8.2 前端调用示例 (JavaScript)

```javascript
// 风险规则查询 API 封装
class RiskRuleAPI {
  
  constructor(baseUrl) {
    this.baseUrl = baseUrl;
    this.servicePath = '/sap/opu/odata/sap/ZUI_RISK_RULE_SRV';
  }
  
  /**
   * 查询风险规则
   * @param {Object} filters - 筛选条件
   * @returns {Promise} - 返回匹配的风险规则列表
   */
  async queryRiskRules(filters) {
    
    const filterParts = [];
    
    // 品类筛选
    if (filters.classA && filters.classA.length > 0) {
      const classValues = filters.classA.map(v => `'${v}'`).join(',');
      filterParts.push(`ClassA in (${classValues})`);
    }
    
    if (filters.classB && filters.classB.length > 0) {
      const classValues = filters.classB.map(v => `'${v}'`).join(',');
      filterParts.push(`ClassB in (${classValues})`);
    }
    
    // 零件品牌筛选
    if (filters.partsBrand && filters.partsBrand.length > 0) {
      const brandValues = filters.partsBrand.map(v => `'${v}'`).join(',');
      filterParts.push(`PartsBrand in (${brandValues})`);
    }
    
    // 品质筛选
    if (filters.quality && filters.quality.length > 0) {
      const qualityValues = filters.quality.map(v => `'${v}'`).join(',');
      filterParts.push(`Quality in (${qualityValues})`);
    }
    
    // 汽车品牌筛选
    if (filters.carBrand && filters.carBrand.length > 0) {
      const brandValues = filters.carBrand.map(v => `'${v}'`).join(',');
      filterParts.push(`CarBrand in (${brandValues})`);
    }
    
    // 价格区间筛选
    if (filters.priceRange && filters.priceRange.length > 0) {
      const priceValues = filters.priceRange.map(v => `'${v}'`).join(',');
      filterParts.push(`PriceRange in (${priceValues})`);
    }
    
    // 连队筛选
    if (filters.region && filters.region.length > 0) {
      const regionValues = filters.region.map(v => `'${v}'`).join(',');
      filterParts.push(`Region in (${regionValues})`);
    }
    
    // 省市区筛选
    if (filters.province) {
      filterParts.push(`ProvinceCode eq '${filters.province}'`);
    }
    if (filters.city) {
      filterParts.push(`CityCode eq '${filters.city}'`);
    }
    if (filters.county) {
      filterParts.push(`CountyCode eq '${filters.county}'`);
    }
    
    // 客户筛选
    if (filters.customer && filters.customer.length > 0) {
      const customerValues = filters.customer.map(v => `'${v}'`).join(',');
      filterParts.push(`CustomerId in (${customerValues})`);
    }
    
    // 供应商筛选
    if (filters.store && filters.store.length > 0) {
      const storeValues = filters.store.map(v => `'${v}'`).join(',');
      filterParts.push(`StoreId in (${storeValues})`);
    }
    
    // 车辆等级筛选
    if (filters.carGrade && filters.carGrade.length > 0) {
      const gradeValues = filters.carGrade.map(v => `'${v}'`).join(',');
      filterParts.push(`CarGrade in (${gradeValues})`);
    }
    
    // 只查询启用的规则
    filterParts.push(`Status eq 'A'`);
    
    // 构建完整的 URL
    const filterString = filterParts.join(' and ');
    const url = `${this.baseUrl}${this.servicePath}/RiskRuleSet` +
                `?$filter=${encodeURIComponent(filterString)}` +
                `&$expand=_Files` +
                `&$orderby=Priority desc` +
                `&$format=json`;
    
    try {
      const response = await fetch(url, {
        method: 'GET',
        headers: {
          'Accept': 'application/json',
          'Content-Type': 'application/json'
        }
      });
      
      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }
      
      const data = await response.json();
      return data.d.results;
      
    } catch (error) {
      console.error('查询风险规则失败:', error);
      throw error;
    }
  }
  
  /**
   * 实际应用示例:订单提交前检查风险
   */
  async checkOrderRisk(orderData) {
    
    const filters = {
      classA: [orderData.productClassA],
      classB: [orderData.productClassB],
      partsBrand: [orderData.partsBrand],
      quality: [orderData.quality],
      carBrand: [orderData.carBrand],
      priceRange: [this.getPriceRangeCode(orderData.price)],
      province: orderData.province,
      city: orderData.city,
      county: orderData.county,
      customer: [orderData.customerId],
      store: [orderData.storeId],
      carGrade: [orderData.carGrade]
    };
    
    const riskRules = await this.queryRiskRules(filters);
    
    if (riskRules && riskRules.length > 0) {
      // 存在风险,返回风险提示
      return {
        hasRisk: true,
        riskCount: riskRules.length,
        risks: riskRules.map(rule => ({
          title: rule.RiskTitle,
          content: rule.RiskContent,
          priority: rule.Priority,
          files: rule._Files.results
        }))
      };
    } else {
      // 无风险
      return {
        hasRisk: false,
        riskCount: 0,
        risks: []
      };
    }
  }
  
  /**
   * 根据价格获取价格区间编码
   */
  getPriceRangeCode(price) {
    if (price <= 100) return 'RANGE01';
    if (price <= 500) return 'RANGE02';
    if (price <= 1000) return 'RANGE03';
    if (price <= 2000) return 'RANGE04';
    if (price <= 3000) return 'RANGE05';
    return 'RANGE06';
  }
}

// 使用示例
const riskAPI = new RiskRuleAPI('https://your-sap-server.com');

// 订单提交前检查
const orderData = {
  productClassA: 'CAT001',
  productClassB: 'CAT001-01',
  partsBrand: 'BRAND001',
  quality: 'OEM',
  carBrand: 'BMW',
  price: 1500,
  province: '110000',
  city: '110100',
  county: '110101',
  customerId: 'CUS001',
  storeId: 'STORE001',
  carGrade: 'HIGH'
};

riskAPI.checkOrderRisk(orderData)
  .then(result => {
    if (result.hasRisk) {
      console.log(`检测到 ${result.riskCount} 个风险提示:`, result.risks);
      // 显示风险提示弹窗
      showRiskWarning(result.risks);
    } else {
      console.log('未检测到风险,可以继续提交订单');
      // 继续提交订单
      submitOrder(orderData);
    }
  })
  .catch(error => {
    console.error('风险检查失败:', error);
  });
```

---

## 九、测试计划

### 9.1 单元测试用例

1. **测试单一条件查询**
   - 输入:品类 = CAT001
   - 预期:返回所有包含 CAT001 的规则

2. **测试多条件组合查询**
   - 输入:品类=CAT001, 品牌=BRAND001, 价格区间=RANGE02
   - 预期:返回同时满足三个条件的规则

3. **测试多选条件**
   - 输入:品类 IN (CAT001, CAT002, CAT003)
   - 预期:返回包含任一品类的规则

4. **测试省市区联动**
   - 输入:省=110000, 市=110100, 区=110101
   - 预期:返回该地区的规则

5. **测试空值处理**
   - 输入:所有条件为空
   - 预期:返回所有启用的规则

6. **测试权限控制**
   - 输入:无权限用户
   - 预期:返回403错误

### 9.2 集成测试

1. **订单提交流程集成**
   - 模拟订单提交
   - 检查风险规则匹配
   - 显示风险提示

2. **询价单流程集成**
   - 模拟询价单创建
   - 实时检查风险
   - 记录风险日志

### 9.3 性能测试

1. **并发查询测试**
   - 100个并发查询
   - 响应时间 < 2秒

2. **大数据量测试**
   - 规则数量:10000+
   - 查询响应时间 < 3秒

---

## 十、部署清单

### 10.1 数据库对象
- [ ] ZTINT_RISK_RULE (主表)
- [ ] ZTINT_RISK_FILE (附件表)
- [ ] ZTINT_PRICE_RANGE (价格区间配置表)

### 10.2 CDS View
- [ ] ZI_RISK_RULE
- [ ] ZI_RISK_FILE

### 10.3 OData Service
- [ ] ZSD_RISK_RULE (Service Definition)
- [ ] ZSB_RISK_RULE_O4 (Service Binding)

### 10.4 ABAP 类
- [ ] ZCL_RISK_RULE_DPC_EXT (OData 实现类)
- [ ] ZCL_RISK_RULE_HELPER (辅助工具类)
- [ ] ZCL_RISK_MATCHER (风险匹配类)

### 10.5 权限对象
- [ ] Z_RISK_RULE (风险规则查询权限)

### 10.6 配置项
- [ ] 价格区间初始数据
- [ ] 测试数据

---

## 十一、注意事项

### 11.1 性能优化建议

1. **索引创建**
   - 在 status, valid_from, valid_to 上创建索引
   - 在常用查询字段上创建组合索引

2. **缓存策略**
   - 对不常变化的规则启用缓存
   - 设置合理的缓存失效时间

3. **数据归档**
   - 定期归档失效的历史规则
   - 保持活动数据量在合理范围

### 11.2 安全性考虑

1. **输入验证**
   - 对所有输入参数进行验证
   - 防止 SQL 注入

2. **权限控制**
   - 实现基于角色的访问控制
   - 敏感数据脱敏

3. **审计日志**
   - 记录所有查询操作
   - 记录规则配置变更

### 11.3 扩展性设计

1. **规则引擎**
   - 支持更复杂的规则逻辑
   - 支持规则优先级和冲突解决

2. **通知机制**
   - 集成企业微信/钉钉通知
   - 支持邮件/短信提醒

3. **数据分析**
   - 风险命中统计
   - 规则有效性分析

---

## 十二、技术文档参考

1. SAP OData Service Development Guide
2. CDS View Best Practices
3. ABAP RESTful Programming Model (RAP)
4. Gateway Service Builder Documentation

---

**文档版本**: v1.0  
**创建日期**: 2025-11-03  
**最后更新**: 2025-11-03  
**文档状态**: 待评审

