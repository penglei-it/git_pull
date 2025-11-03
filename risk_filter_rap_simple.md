# 风险提示配置 - RAP 简化实现方案

## 适用场景
如果您的SAP系统支持 **ABAP RESTful Application Programming Model (RAP)**,推荐使用这种更现代化的实现方式。

---

## 一、CDS 数据模型定义

### 1.1 基础接口视图 (Interface View)

```abap
@AccessControl.authorizationCheck: #CHECK
@EndUserText.label: 'Risk Rule - Interface View'
define root view entity ZI_RISK_RULE
  as select from ztint_risk_rule
  
  composition [0..*] of ZI_RISK_FILE as _Files
  
{
  key rule_id           as RuleId,
  
      // 基本信息
      rule_name         as RuleName,
      rule_desc         as RuleDesc,
      status            as Status,
      priority          as Priority,
      
      // 风险内容
      risk_title        as RiskTitle,
      risk_content      as RiskContent,
      
      // 筛选条件 - 支持多选查询
      @Consumption.filter: {selectionType: #INTERVAL, multipleSelections: true}
      class_a           as ClassA,
      
      @Consumption.filter: {selectionType: #INTERVAL, multipleSelections: true}
      class_b           as ClassB,
      
      @Consumption.filter: {selectionType: #INTERVAL, multipleSelections: true}
      class_c           as ClassC,
      
      @Consumption.filter: {selectionType: #INTERVAL, multipleSelections: true}
      class_d           as ClassD,
      
      @Consumption.filter: {selectionType: #INTERVAL, multipleSelections: true}
      parts_brand       as PartsBrand,
      
      @Consumption.filter: {selectionType: #INTERVAL, multipleSelections: true}
      quality           as Quality,
      
      @Consumption.filter: {selectionType: #INTERVAL, multipleSelections: true}
      car_brand         as CarBrand,
      
      @Consumption.filter: {selectionType: #INTERVAL, multipleSelections: true}
      price_range       as PriceRange,
      
      @Consumption.filter: {selectionType: #INTERVAL, multipleSelections: true}
      region            as Region,
      
      @Consumption.filter: {selectionType: #INTERVAL, multipleSelections: true}
      province_code     as ProvinceCode,
      
      @Consumption.filter: {selectionType: #INTERVAL, multipleSelections: true}
      city_code         as CityCode,
      
      @Consumption.filter: {selectionType: #INTERVAL, multipleSelections: true}
      county_code       as CountyCode,
      
      @Consumption.filter: {selectionType: #INTERVAL, multipleSelections: true}
      village_code      as VillageCode,
      
      @Consumption.filter: {selectionType: #INTERVAL, multipleSelections: true}
      customer_id       as CustomerId,
      
      @Consumption.filter: {selectionType: #INTERVAL, multipleSelections: true}
      store_id          as StoreId,
      
      @Consumption.filter: {selectionType: #INTERVAL, multipleSelections: true}
      car_grade         as CarGrade,
      
      is_strict         as IsStrict,
      
      // 有效期
      @Consumption.filter: {selectionType: #INTERVAL}
      valid_from        as ValidFrom,
      
      @Consumption.filter: {selectionType: #INTERVAL}
      valid_to          as ValidTo,
      
      // 审计字段
      @Semantics.systemDateTime.createdAt: true
      zcrt_bdate        as CreatedDate,
      zcrt_btime        as CreatedTime,
      
      @Semantics.user.createdBy: true
      zcrt_uname        as CreatedBy,
      
      @Semantics.systemDateTime.lastChangedAt: true
      zupd_bdate        as UpdatedDate,
      zupd_btime        as UpdatedTime,
      
      @Semantics.user.lastChangedBy: true
      zupd_uname        as UpdatedBy,
      
      // 关联
      _Files
}
```

### 1.2 附件视图

```abap
@AccessControl.authorizationCheck: #CHECK
@EndUserText.label: 'Risk File - Interface View'
define view entity ZI_RISK_FILE
  as select from ztint_risk_file
  
  association to parent ZI_RISK_RULE as _Rule
    on $projection.RuleId = _Rule.RuleId
  
{
  key rule_id         as RuleId,
  key file_id         as FileId,
  
      file_name       as FileName,
      file_type       as FileType,
      file_url        as FileUrl,
      file_size       as FileSize,
      
      zcrt_bdate      as CreatedDate,
      zcrt_btime      as CreatedTime,
      zcrt_uname      as CreatedBy,
      
      // 关联
      _Rule
}
```

### 1.3 消费视图 (Consumption View)

```abap
@AccessControl.authorizationCheck: #CHECK
@EndUserText.label: 'Risk Rule - Consumption View'
@Metadata.allowExtensions: true
@Search.searchable: true

define root view entity ZC_RISK_RULE
  provider contract transactional_query
  as projection on ZI_RISK_RULE
{
  key RuleId,
  
      @Search.defaultSearchElement: true
      @Search.fuzzinessThreshold: 0.8
      RuleName,
      
      @Search.defaultSearchElement: true
      RuleDesc,
      
      @ObjectModel.text.element: ['StatusText']
      Status,
      
      // 虚拟字段 - 状态描述
      case Status
        when 'A' then '启用'
        when 'D' then '停用'
        else ''
      end as StatusText,
      
      Priority,
      RiskTitle,
      RiskContent,
      
      // 筛选条件字段
      ClassA,
      ClassB,
      ClassC,
      ClassD,
      PartsBrand,
      Quality,
      CarBrand,
      PriceRange,
      Region,
      ProvinceCode,
      CityCode,
      CountyCode,
      VillageCode,
      CustomerId,
      StoreId,
      CarGrade,
      IsStrict,
      
      ValidFrom,
      ValidTo,
      
      CreatedDate,
      CreatedTime,
      CreatedBy,
      UpdatedDate,
      UpdatedTime,
      UpdatedBy,
      
      // 导航
      _Files : redirected to composition child ZC_RISK_FILE
}
```

### 1.4 附件消费视图

```abap
@AccessControl.authorizationCheck: #CHECK
@EndUserText.label: 'Risk File - Consumption View'
@Metadata.allowExtensions: true

define view entity ZC_RISK_FILE
  as projection on ZI_RISK_FILE
{
  key RuleId,
  key FileId,
  
      FileName,
      
      @ObjectModel.text.element: ['FileTypeText']
      FileType,
      
      // 虚拟字段 - 文件类型描述
      case FileType
        when 'IMG'   then '图片'
        when 'VIDEO' then '视频'
        when 'PDF'   then 'PDF文档'
        else '其他'
      end as FileTypeText,
      
      FileUrl,
      FileSize,
      
      CreatedDate,
      CreatedTime,
      CreatedBy,
      
      // 导航
      _Rule : redirected to parent ZC_RISK_RULE
}
```

---

## 二、行为定义 (Behavior Definition)

### 2.1 主实体行为定义

```abap
managed implementation in class zbp_i_risk_rule unique;
strict ( 2 );

define behavior for ZI_RISK_RULE alias RiskRule
persistent table ztint_risk_rule
lock master
authorization master ( instance )
etag master UpdatedDate
{
  // 标准操作
  create;
  update;
  delete;
  
  // 字段控制
  field ( readonly ) RuleId, CreatedDate, CreatedTime, CreatedBy;
  field ( mandatory ) RuleName, Status;
  
  // 映射
  mapping for ztint_risk_rule
  {
    RuleId = rule_id;
    RuleName = rule_name;
    RuleDesc = rule_desc;
    Status = status;
    Priority = priority;
    RiskTitle = risk_title;
    RiskContent = risk_content;
    ClassA = class_a;
    ClassB = class_b;
    ClassC = class_c;
    ClassD = class_d;
    PartsBrand = parts_brand;
    Quality = quality;
    CarBrand = car_brand;
    PriceRange = price_range;
    Region = region;
    ProvinceCode = province_code;
    CityCode = city_code;
    CountyCode = county_code;
    VillageCode = village_code;
    CustomerId = customer_id;
    StoreId = store_id;
    CarGrade = car_grade;
    IsStrict = is_strict;
    ValidFrom = valid_from;
    ValidTo = valid_to;
    CreatedDate = zcrt_bdate;
    CreatedTime = zcrt_btime;
    CreatedBy = zcrt_uname;
    UpdatedDate = zupd_bdate;
    UpdatedTime = zupd_btime;
    UpdatedBy = zupd_uname;
  }
  
  // 子实体组合关系
  association _Files { create; delete; }
  
  // 自定义操作
  action activate result [1] $self;
  action deactivate result [1] $self;
  
  // 验证
  validation validateDates on save { field ValidFrom, ValidTo; }
  validation validateStatus on save { field Status; }
  
  // 确定
  determination setInitialValues on modify { create; }
  determination setPriority on modify { create; }
}

define behavior for ZI_RISK_FILE alias RiskFile
persistent table ztint_risk_file
lock dependent by _Rule
authorization dependent by _Rule
etag master CreatedDate
{
  update;
  delete;
  
  field ( readonly ) RuleId, FileId, CreatedDate, CreatedTime, CreatedBy;
  field ( mandatory ) FileName, FileType, FileUrl;
  
  mapping for ztint_risk_file
  {
    RuleId = rule_id;
    FileId = file_id;
    FileName = file_name;
    FileType = file_type;
    FileUrl = file_url;
    FileSize = file_size;
    CreatedDate = zcrt_bdate;
    CreatedTime = zcrt_btime;
    CreatedBy = zcrt_uname;
  }
  
  association _Rule;
}
```

### 2.2 投影行为定义

```abap
projection;
strict ( 2 );

define behavior for ZC_RISK_RULE alias RiskRule
{
  use create;
  use update;
  use delete;
  
  use action activate;
  use action deactivate;
  
  use association _Files { create; delete; }
}

define behavior for ZC_RISK_FILE alias RiskFile
{
  use update;
  use delete;
  
  use association _Rule;
}
```

---

## 三、行为实现类

### 3.1 主类实现

```abap
CLASS zbp_i_risk_rule DEFINITION
  PUBLIC
  ABSTRACT
  FINAL
  FOR BEHAVIOR OF zi_risk_rule.

ENDCLASS.

CLASS zbp_i_risk_rule IMPLEMENTATION.
ENDCLASS.
```

### 3.2 本地类实现

```abap
CLASS lhc_riskrule DEFINITION INHERITING FROM cl_abap_behavior_handler.

  PRIVATE SECTION.
    
    METHODS get_instance_authorizations FOR INSTANCE AUTHORIZATION
      IMPORTING keys REQUEST requested_authorizations FOR riskrule RESULT result.
      
    METHODS setInitialValues FOR DETERMINE ON MODIFY
      IMPORTING keys FOR riskrule~setInitialValues.
      
    METHODS setPriority FOR DETERMINE ON MODIFY
      IMPORTING keys FOR riskrule~setPriority.
      
    METHODS validateDates FOR VALIDATE ON SAVE
      IMPORTING keys FOR riskrule~validateDates.
      
    METHODS validateStatus FOR VALIDATE ON SAVE
      IMPORTING keys FOR riskrule~validateStatus.
      
    METHODS activate FOR MODIFY
      IMPORTING keys FOR ACTION riskrule~activate RESULT result.
      
    METHODS deactivate FOR MODIFY
      IMPORTING keys FOR ACTION riskrule~deactivate RESULT result.

ENDCLASS.

CLASS lhc_riskrule IMPLEMENTATION.

  METHOD get_instance_authorizations.
    " 权限检查实现
  ENDMETHOD.

  METHOD setInitialValues.
    
    " 读取需要设置初始值的实体
    READ ENTITIES OF zi_risk_rule IN LOCAL MODE
      ENTITY riskrule
        FIELDS ( RuleId Status ValidFrom )
        WITH CORRESPONDING #( keys )
      RESULT DATA(rules).
    
    " 删除已有值的记录
    DELETE rules WHERE RuleId IS NOT INITIAL.
    CHECK rules IS NOT INITIAL.
    
    " 生成 GUID 并设置默认值
    MODIFY ENTITIES OF zi_risk_rule IN LOCAL MODE
      ENTITY riskrule
        UPDATE
          FIELDS ( RuleId Status ValidFrom )
          WITH VALUE #( FOR rule IN rules (
            %tky = rule-%tky
            RuleId = cl_system_uuid=>create_uuid_c32( )
            Status = 'A'
            ValidFrom = sy-datum
          ) ).
          
  ENDMETHOD.

  METHOD setPriority.
    
    " 读取实体
    READ ENTITIES OF zi_risk_rule IN LOCAL MODE
      ENTITY riskrule
        FIELDS ( Priority )
        WITH CORRESPONDING #( keys )
      RESULT DATA(rules).
    
    " 如果没有设置优先级,则设置默认值
    MODIFY ENTITIES OF zi_risk_rule IN LOCAL MODE
      ENTITY riskrule
        UPDATE
          FIELDS ( Priority )
          WITH VALUE #( FOR rule IN rules WHERE ( Priority IS INITIAL ) (
            %tky = rule-%tky
            Priority = 50
          ) ).
          
  ENDMETHOD.

  METHOD validateDates.
    
    " 读取数据
    READ ENTITIES OF zi_risk_rule IN LOCAL MODE
      ENTITY riskrule
        FIELDS ( ValidFrom ValidTo )
        WITH CORRESPONDING #( keys )
      RESULT DATA(rules).
    
    " 验证日期有效性
    LOOP AT rules INTO DATA(rule).
      
      IF rule-ValidFrom IS NOT INITIAL AND rule-ValidTo IS NOT INITIAL.
        IF rule-ValidFrom > rule-ValidTo.
          
          APPEND VALUE #(
            %tky = rule-%tky
            %msg = new_message_with_text(
              severity = if_abap_behv_message=>severity-error
              text = '生效日期不能晚于失效日期'
            )
            %element-ValidFrom = if_abap_behv=>mk-on
            %element-ValidTo = if_abap_behv=>mk-on
          ) TO reported-riskrule.
          
          APPEND VALUE #(
            %tky = rule-%tky
          ) TO failed-riskrule.
          
        ENDIF.
      ENDIF.
      
    ENDLOOP.
    
  ENDMETHOD.

  METHOD validateStatus.
    
    " 读取数据
    READ ENTITIES OF zi_risk_rule IN LOCAL MODE
      ENTITY riskrule
        FIELDS ( Status )
        WITH CORRESPONDING #( keys )
      RESULT DATA(rules).
    
    " 验证状态有效性
    LOOP AT rules INTO DATA(rule).
      
      IF rule-Status NOT IN ( 'A', 'D' ).
        
        APPEND VALUE #(
          %tky = rule-%tky
          %msg = new_message_with_text(
            severity = if_abap_behv_message=>severity-error
            text = |状态值无效: { rule-Status }|
          )
          %element-Status = if_abap_behv=>mk-on
        ) TO reported-riskrule.
        
        APPEND VALUE #(
          %tky = rule-%tky
        ) TO failed-riskrule.
        
      ENDIF.
      
    ENDLOOP.
    
  ENDMETHOD.

  METHOD activate.
    
    " 读取实体
    READ ENTITIES OF zi_risk_rule IN LOCAL MODE
      ENTITY riskrule
        ALL FIELDS WITH CORRESPONDING #( keys )
      RESULT DATA(rules).
    
    " 激活规则
    MODIFY ENTITIES OF zi_risk_rule IN LOCAL MODE
      ENTITY riskrule
        UPDATE
          FIELDS ( Status )
          WITH VALUE #( FOR rule IN rules (
            %tky = rule-%tky
            Status = 'A'
          ) )
      MAPPED DATA(mapped_rules)
      REPORTED DATA(reported_rules)
      FAILED DATA(failed_rules).
    
    " 返回结果
    READ ENTITIES OF zi_risk_rule IN LOCAL MODE
      ENTITY riskrule
        ALL FIELDS WITH CORRESPONDING #( keys )
      RESULT rules.
    
    result = VALUE #( FOR rule IN rules (
      %tky = rule-%tky
      %param = rule
    ) ).
    
  ENDMETHOD.

  METHOD deactivate.
    
    " 读取实体
    READ ENTITIES OF zi_risk_rule IN LOCAL MODE
      ENTITY riskrule
        ALL FIELDS WITH CORRESPONDING #( keys )
      RESULT DATA(rules).
    
    " 停用规则
    MODIFY ENTITIES OF zi_risk_rule IN LOCAL MODE
      ENTITY riskrule
        UPDATE
          FIELDS ( Status )
          WITH VALUE #( FOR rule IN rules (
            %tky = rule-%tky
            Status = 'D'
          ) )
      MAPPED DATA(mapped_rules)
      REPORTED DATA(reported_rules)
      FAILED DATA(failed_rules).
    
    " 返回结果
    READ ENTITIES OF zi_risk_rule IN LOCAL MODE
      ENTITY riskrule
        ALL FIELDS WITH CORRESPONDING #( keys )
      RESULT rules.
    
    result = VALUE #( FOR rule IN rules (
      %tky = rule-%tky
      %param = rule
    ) ).
    
  ENDMETHOD.

ENDCLASS.
```

---

## 四、服务定义与绑定

### 4.1 服务定义

```abap
@EndUserText.label: 'Risk Rule Service Definition'
define service ZUI_RISK_RULE_O4 {
  expose ZC_RISK_RULE as RiskRule;
  expose ZC_RISK_FILE as RiskFile;
}
```

### 4.2 服务绑定

在 ADT 中:
1. 右键服务定义 `ZUI_RISK_RULE_O4`
2. 选择 "New Service Binding"
3. 选择 Binding Type: `OData V4 - UI`
4. 输入名称: `ZUI_RISK_RULE_O4`
5. 激活并发布服务

---

## 五、使用示例

### 5.1 ABAP 调用示例

```abap
" 创建规则
DATA: lt_rules TYPE TABLE FOR CREATE zi_risk_rule.

lt_rules = VALUE #(
  ( %cid = 'RULE001'
    RuleName = '高风险零件提示'
    RuleDesc = '针对高价值零件的风险提示'
    Status = 'A'
    Priority = 100
    RiskTitle = '高价值零件风险提示'
    RiskContent = '该零件价格较高,请谨慎核对规格和质量'
    ClassA = 'CAT001,CAT002'
    PriceRange = 'RANGE05,RANGE06'
    ValidFrom = sy-datum
    ValidTo = '99991231'
  )
).

MODIFY ENTITIES OF zi_risk_rule
  ENTITY riskrule
    CREATE FIELDS ( RuleName RuleDesc Status Priority RiskTitle RiskContent
                    ClassA PriceRange ValidFrom ValidTo )
    WITH lt_rules
  MAPPED DATA(mapped)
  FAILED DATA(failed)
  REPORTED DATA(reported).

IF failed IS INITIAL.
  COMMIT ENTITIES.
  MESSAGE '规则创建成功' TYPE 'S'.
ELSE.
  MESSAGE '规则创建失败' TYPE 'E'.
ENDIF.
```

### 5.2 查询示例

```abap
" 查询符合条件的规则
SELECT FROM ZC_RISK_RULE
  FIELDS RuleId, RuleName, RiskTitle, RiskContent, Priority
  WHERE Status = 'A'
    AND ClassA LIKE '%CAT001%'
    AND PriceRange LIKE '%RANGE05%'
  ORDER BY Priority DESCENDING
  INTO TABLE @DATA(lt_matched_rules).

IF sy-subrc = 0.
  LOOP AT lt_matched_rules INTO DATA(ls_rule).
    WRITE: / ls_rule-RuleName, ls_rule-RiskTitle.
  ENDLOOP.
ENDIF.
```

---

## 六、优势对比

### RAP 架构 vs 传统 Gateway

| 特性 | RAP | 传统 Gateway |
|------|-----|--------------|
| 开发效率 | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| 代码量 | 少 (约30%) | 多 |
| 类型安全 | 编译期检查 | 运行时检查 |
| 事务处理 | 内置 Managed | 手动实现 |
| 权限控制 | 声明式 | 编程式 |
| 版本支持 | OData V4 | OData V2 |
| 学习曲线 | 陡峭 | 平缓 |

---

## 七、总结

RAP 方式实现的优势:
✅ **代码量更少** - 约减少 60-70% 的样板代码
✅ **类型安全** - 编译期类型检查
✅ **标准化** - 遵循SAP标准开发模式
✅ **可维护性** - 结构清晰,易于维护
✅ **性能优化** - 框架自动优化查询

适用版本:
- SAP S/4HANA 1909 及以上
- SAP NetWeaver 7.54 及以上

---

**推荐**: 如果您的系统支持,强烈推荐使用 RAP 架构!

