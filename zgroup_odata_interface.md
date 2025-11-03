# ZGROUP 风险提示配置 OData 接口设计

## 一、业务概述

### 1.1 接口用途
设计一套 OData 接口，支持风险提示规则的查询和配置管理，包含以下10个核心筛选维度：

| 维度序号 | 中文名称 | 字段名称 | 说明 |
|---------|---------|---------|------|
| 1 | 品类 | CLASS | 支持多级品类筛选 |
| 2 | 零件品牌 | PARTSBRAND | 支持多选 |
| 3 | 品质 | QUALITY | 如：OEM、副厂等 |
| 4 | 汽车品牌 | CARBRAND | 支持多选 |
| 5 | 价格区间 | PRICE | 预设6个离散区间 |
| 6 | 连队 | REGION | 区域维度1 |
| 7 | 省市区 | ZONE | 区域维度2(省/市/区县) |
| 8 | 维修厂(客户) | CUS | 买方客户 |
| 9 | 供应商(商家) | STORE | 卖方店铺 |
| 10 | 车辆等级 | CARGRADE | 如：高端车、中端车 |

---

## 二、数据库表设计

### 2.1 主表: ZTINT_RISK_GROUP

```abap
@EndUserText.label : '风险组配置主表'
@AbapCatalog.tableCategory : #TRANSPARENT
define table ztint_risk_group {
  
  key client          : abap.clnt not null;
  key group_id        : abap.char(32) not null;    // 规则组ID (GUID)
  
  // 基本信息
  group_code          : abap.char(20);             // 组编码
  group_name          : abap.char(100);            // 组名称
  group_desc          : abap.string(0);            // 组描述
  
  // 筛选条件 - 支持多值(逗号分隔)
  class_filter        : abap.string(0);            // 品类筛选
  partsbrand_filter   : abap.string(0);            // 零件品牌筛选
  quality_filter      : abap.string(0);            // 品质筛选
  carbrand_filter     : abap.string(0);            // 汽车品牌筛选
  price_filter        : abap.string(0);            // 价格区间筛选
  region_filter       : abap.string(0);            // 连队筛选
  zone_province       : abap.string(0);            // 省份筛选
  zone_city           : abap.string(0);            // 城市筛选
  zone_county         : abap.string(0);            // 区县筛选
  cus_filter          : abap.string(0);            // 客户筛选
  store_filter        : abap.string(0);            // 供应商筛选
  cargrade_filter     : abap.string(0);            // 车辆等级筛选
  
  // 风险内容
  risk_title          : abap.char(100);            // 风险标题
  risk_content        : abap.string(0);            // 风险内容
  
  // 状态管理
  status              : abap.char(1);              // A-启用 D-停用
  priority            : abap.int4;                 // 优先级
  
  // 有效期
  valid_from          : abap.dats;                 // 生效日期
  valid_to            : abap.dats;                 // 失效日期
  
  // 审计字段
  created_by          : abap.char(12);             // 创建人
  created_at          : abap.dats;                 // 创建日期
  created_time        : abap.tims;                 // 创建时间
  changed_by          : abap.char(12);             // 修改人
  changed_at          : abap.dats;                 // 修改日期
  changed_time        : abap.tims;                 // 修改时间
  
}
```

### 2.2 附件表: ZTINT_RISK_ATTACH

```abap
@EndUserText.label : '风险组附件表'
define table ztint_risk_attach {
  
  key client          : abap.clnt not null;
  key group_id        : abap.char(32) not null;    // 关联主表
  key attach_id       : abap.char(32) not null;    // 附件ID (GUID)
  
  file_name           : abap.char(255);            // 文件名
  file_type           : abap.char(10);             // IMG/VIDEO/PDF/DOC
  file_url            : abap.string(0);            // 文件URL
  file_size           : abap.int8;                 // 文件大小(字节)
  mime_type           : abap.char(100);            // MIME类型
  
  created_by          : abap.char(12);
  created_at          : abap.dats;
  created_time        : abap.tims;
  
}
```

---

## 三、CDS View 定义 (RAP方式)

### 3.1 基础接口视图

```abap
@AccessControl.authorizationCheck: #CHECK
@EndUserText.label: 'Risk Group - Interface View'

define root view entity ZI_RISK_GROUP
  as select from ztint_risk_group
  
  composition [0..*] of ZI_RISK_ATTACH as _Attachments
  
{
  key group_id                  as GroupId,
  
      // 基本信息
      group_code                as GroupCode,
      
      @Semantics.text: true
      group_name                as GroupName,
      
      @Semantics.text: true
      group_desc                as GroupDesc,
      
      // 筛选条件 - 所有字段支持多选查询
      @Consumption.filter: { selectionType: #INTERVAL, multipleSelections: true }
      class_filter              as ClassFilter,
      
      @Consumption.filter: { selectionType: #INTERVAL, multipleSelections: true }
      partsbrand_filter         as PartsBrandFilter,
      
      @Consumption.filter: { selectionType: #INTERVAL, multipleSelections: true }
      quality_filter            as QualityFilter,
      
      @Consumption.filter: { selectionType: #INTERVAL, multipleSelections: true }
      carbrand_filter           as CarBrandFilter,
      
      @Consumption.filter: { selectionType: #INTERVAL, multipleSelections: true }
      price_filter              as PriceFilter,
      
      @Consumption.filter: { selectionType: #INTERVAL, multipleSelections: true }
      region_filter             as RegionFilter,
      
      @Consumption.filter: { selectionType: #INTERVAL, multipleSelections: true }
      zone_province             as ZoneProvince,
      
      @Consumption.filter: { selectionType: #INTERVAL, multipleSelections: true }
      zone_city                 as ZoneCity,
      
      @Consumption.filter: { selectionType: #INTERVAL, multipleSelections: true }
      zone_county               as ZoneCounty,
      
      @Consumption.filter: { selectionType: #INTERVAL, multipleSelections: true }
      cus_filter                as CusFilter,
      
      @Consumption.filter: { selectionType: #INTERVAL, multipleSelections: true }
      store_filter              as StoreFilter,
      
      @Consumption.filter: { selectionType: #INTERVAL, multipleSelections: true }
      cargrade_filter           as CarGradeFilter,
      
      // 风险内容
      risk_title                as RiskTitle,
      risk_content              as RiskContent,
      
      // 状态管理
      status                    as Status,
      priority                  as Priority,
      
      // 有效期
      valid_from                as ValidFrom,
      valid_to                  as ValidTo,
      
      // 审计字段
      @Semantics.user.createdBy: true
      created_by                as CreatedBy,
      
      @Semantics.systemDate.createdAt: true
      created_at                as CreatedAt,
      
      created_time              as CreatedTime,
      
      @Semantics.user.lastChangedBy: true
      changed_by                as ChangedBy,
      
      @Semantics.systemDate.lastChangedAt: true
      changed_at                as ChangedAt,
      
      changed_time              as ChangedTime,
      
      // 关联
      _Attachments
}
```

### 3.2 附件视图

```abap
@AccessControl.authorizationCheck: #CHECK
@EndUserText.label: 'Risk Attachment - Interface View'

define view entity ZI_RISK_ATTACH
  as select from ztint_risk_attach
  
  association to parent ZI_RISK_GROUP as _Group
    on $projection.GroupId = _Group.GroupId
  
{
  key group_id          as GroupId,
  key attach_id         as AttachId,
  
      file_name         as FileName,
      file_type         as FileType,
      file_url          as FileUrl,
      file_size         as FileSize,
      mime_type         as MimeType,
      
      created_by        as CreatedBy,
      created_at        as CreatedAt,
      created_time      as CreatedTime,
      
      // 关联
      _Group
}
```

### 3.3 消费视图 (Projection)

```abap
@AccessControl.authorizationCheck: #CHECK
@EndUserText.label: 'Risk Group - Consumption View'
@Metadata.allowExtensions: true
@Search.searchable: true

define root view entity ZC_RISK_GROUP
  provider contract transactional_query
  as projection on ZI_RISK_GROUP
{
  key GroupId,
  
      @Search.defaultSearchElement: true
      @Search.fuzzinessThreshold: 0.8
      GroupCode,
      
      @Search.defaultSearchElement: true
      GroupName,
      
      @Search.defaultSearchElement: true
      GroupDesc,
      
      // 筛选条件
      ClassFilter,
      PartsBrandFilter,
      QualityFilter,
      CarBrandFilter,
      PriceFilter,
      RegionFilter,
      ZoneProvince,
      ZoneCity,
      ZoneCounty,
      CusFilter,
      StoreFilter,
      CarGradeFilter,
      
      // 风险内容
      RiskTitle,
      RiskContent,
      
      // 状态管理
      @ObjectModel.text.element: ['StatusText']
      Status,
      
      // 状态文本 (虚拟字段)
      case Status
        when 'A' then '启用'
        when 'D' then '停用'
        else ''
      end as StatusText,
      
      Priority,
      
      // 有效期
      ValidFrom,
      ValidTo,
      
      // 审计字段
      CreatedBy,
      CreatedAt,
      CreatedTime,
      ChangedBy,
      ChangedAt,
      ChangedTime,
      
      // 导航
      _Attachments : redirected to composition child ZC_RISK_ATTACH
}
```

### 3.4 附件消费视图

```abap
@AccessControl.authorizationCheck: #CHECK
@EndUserText.label: 'Risk Attachment - Consumption View'

define view entity ZC_RISK_ATTACH
  as projection on ZI_RISK_ATTACH
{
  key GroupId,
  key AttachId,
  
      FileName,
      
      @ObjectModel.text.element: ['FileTypeText']
      FileType,
      
      // 文件类型文本
      case FileType
        when 'IMG'   then '图片'
        when 'VIDEO' then '视频'
        when 'PDF'   then 'PDF文档'
        when 'DOC'   then 'Word文档'
        else '其他'
      end as FileTypeText,
      
      FileUrl,
      FileSize,
      MimeType,
      
      CreatedBy,
      CreatedAt,
      CreatedTime,
      
      // 导航
      _Group : redirected to parent ZC_RISK_GROUP
}
```

---

## 四、Behavior Definition (行为定义)

### 4.1 接口层行为定义

```abap
managed implementation in class zbp_i_risk_group unique;
strict ( 2 );
with draft;

define behavior for ZI_RISK_GROUP alias RiskGroup
persistent table ztint_risk_group
draft table ztint_risk_group_d
lock master total etag ChangedAt
authorization master ( instance )
{
  // CRUD操作
  create;
  update;
  delete;
  
  // 草稿支持
  draft action Edit;
  draft action Activate optimized;
  draft action Discard;
  draft action Resume;
  draft determine action Prepare;
  
  // 字段控制
  field ( readonly ) GroupId, CreatedBy, CreatedAt, CreatedTime;
  field ( mandatory ) GroupName, Status;
  
  // 映射
  mapping for ztint_risk_group
  {
    GroupId = group_id;
    GroupCode = group_code;
    GroupName = group_name;
    GroupDesc = group_desc;
    ClassFilter = class_filter;
    PartsBrandFilter = partsbrand_filter;
    QualityFilter = quality_filter;
    CarBrandFilter = carbrand_filter;
    PriceFilter = price_filter;
    RegionFilter = region_filter;
    ZoneProvince = zone_province;
    ZoneCity = zone_city;
    ZoneCounty = zone_county;
    CusFilter = cus_filter;
    StoreFilter = store_filter;
    CarGradeFilter = cargrade_filter;
    RiskTitle = risk_title;
    RiskContent = risk_content;
    Status = status;
    Priority = priority;
    ValidFrom = valid_from;
    ValidTo = valid_to;
    CreatedBy = created_by;
    CreatedAt = created_at;
    CreatedTime = created_time;
    ChangedBy = changed_by;
    ChangedAt = changed_at;
    ChangedTime = changed_time;
  }
  
  // 子实体关联
  association _Attachments { create; delete; }
  
  // 自定义动作
  action activate result [1] $self;
  action deactivate result [1] $self;
  
  // 验证
  validation validateDates on save { field ValidFrom, ValidTo; }
  validation validateStatus on save { field Status; }
  
  // 确定
  determination setInitialValues on modify { create; }
  determination generateGroupId on save { create; }
}

define behavior for ZI_RISK_ATTACH alias RiskAttach
persistent table ztint_risk_attach
draft table ztint_risk_attach_d
lock dependent by _Group
authorization dependent by _Group
{
  update;
  delete;
  
  field ( readonly ) GroupId, AttachId, CreatedBy, CreatedAt;
  field ( mandatory ) FileName, FileType, FileUrl;
  
  mapping for ztint_risk_attach
  {
    GroupId = group_id;
    AttachId = attach_id;
    FileName = file_name;
    FileType = file_type;
    FileUrl = file_url;
    FileSize = file_size;
    MimeType = mime_type;
    CreatedBy = created_by;
    CreatedAt = created_at;
    CreatedTime = created_time;
  }
  
  association _Group;
  
  determination generateAttachId on save { create; }
}
```

### 4.2 投影层行为定义

```abap
projection;
strict ( 2 );
use draft;

define behavior for ZC_RISK_GROUP alias RiskGroup
{
  use create;
  use update;
  use delete;
  
  use action Edit;
  use action Activate;
  use action Discard;
  use action Resume;
  use action Prepare;
  
  use action activate;
  use action deactivate;
  
  use association _Attachments { create; delete; }
}

define behavior for ZC_RISK_ATTACH alias RiskAttach
{
  use update;
  use delete;
  
  use association _Group;
}
```

---

## 五、Service Definition

```abap
@EndUserText.label: 'Risk Group OData Service'
define service ZGROUP_RISK_SRV {
  
  expose ZC_RISK_GROUP as RiskGroup;
  expose ZC_RISK_ATTACH as RiskAttachment;
  
}
```

---

## 六、Service Binding

### 创建步骤:
1. 在 ADT 中右键点击 Service Definition `ZGROUP_RISK_SRV`
2. 选择 **New → Service Binding**
3. 配置如下:
   - **Name**: `ZGROUP_RISK_O4`
   - **Description**: `Risk Group OData V4 Service`
   - **Binding Type**: `OData V4 - UI`
   - **Service Definition**: `ZGROUP_RISK_SRV`
4. 激活并发布

### 服务URL格式:
```
/sap/opu/odata4/sap/zgroup_risk_o4/srvd/sap/zgroup_risk_srv/0001/
```

---

## 七、OData 查询示例

### 7.1 基础查询 - 获取所有启用的规则组

```http
GET /sap/opu/odata4/sap/zgroup_risk_o4/srvd/sap/zgroup_risk_srv/0001/RiskGroup
  ?$filter=Status eq 'A'
  &$orderby=Priority desc
```

### 7.2 单条件查询 - 按品类筛选

```http
GET /sap/opu/odata4/sap/zgroup_risk_o4/srvd/sap/zgroup_risk_srv/0001/RiskGroup
  ?$filter=contains(ClassFilter,'CAT001')
```

### 7.3 多条件组合查询 - 品类 + 价格区间 + 地区

```http
GET /sap/opu/odata4/sap/zgroup_risk_o4/srvd/sap/zgroup_risk_srv/0001/RiskGroup
  ?$filter=contains(ClassFilter,'CAT001') 
    and contains(PriceFilter,'RANGE03')
    and contains(ZoneProvince,'110000')
  &$expand=_Attachments
```

### 7.4 复杂查询 - IN 操作符(多选)

```http
GET /sap/opu/odata4/sap/zgroup_risk_o4/srvd/sap/zgroup_risk_srv/0001/RiskGroup
  ?$filter=(contains(ClassFilter,'CAT001') or contains(ClassFilter,'CAT002'))
    and (contains(CarBrandFilter,'BMW') or contains(CarBrandFilter,'BENZ'))
    and Status eq 'A'
  &$expand=_Attachments
  &$top=20
  &$skip=0
```

### 7.5 地区联动查询 - 省市区三级筛选

```http
GET /sap/opu/odata4/sap/zgroup_risk_o4/srvd/sap/zgroup_risk_srv/0001/RiskGroup
  ?$filter=contains(ZoneProvince,'110000')
    and contains(ZoneCity,'110100')
    and contains(ZoneCounty,'110101')
```

### 7.6 全文搜索

```http
GET /sap/opu/odata4/sap/zgroup_risk_o4/srvd/sap/zgroup_risk_srv/0001/RiskGroup
  ?$search="发动机 风险"
  &$filter=Status eq 'A'
```

### 7.7 获取单个规则及其附件

```http
GET /sap/opu/odata4/sap/zgroup_risk_o4/srvd/sap/zgroup_risk_srv/0001/RiskGroup(GroupId='A1B2C3D4...')
  ?$expand=_Attachments
```

### 7.8 创建新规则 (POST)

```http
POST /sap/opu/odata4/sap/zgroup_risk_o4/srvd/sap/zgroup_risk_srv/0001/RiskGroup
Content-Type: application/json

{
  "GroupCode": "RISK001",
  "GroupName": "高价值零件风险提示",
  "GroupDesc": "针对高价值零件的风险警告",
  "ClassFilter": "CAT001,CAT002",
  "PriceFilter": "RANGE05,RANGE06",
  "RiskTitle": "高价值零件风险",
  "RiskContent": "该零件价格较高，请仔细核对规格和质量标准",
  "Status": "A",
  "Priority": 100,
  "ValidFrom": "2025-01-01",
  "ValidTo": "9999-12-31"
}
```

### 7.9 更新规则 (PATCH)

```http
PATCH /sap/opu/odata4/sap/zgroup_risk_o4/srvd/sap/zgroup_risk_srv/0001/RiskGroup(GroupId='A1B2C3D4...')
Content-Type: application/json

{
  "Status": "D",
  "RiskContent": "更新后的风险内容"
}
```

### 7.10 删除规则 (DELETE)

```http
DELETE /sap/opu/odata4/sap/zgroup_risk_o4/srvd/sap/zgroup_risk_srv/0001/RiskGroup(GroupId='A1B2C3D4...')
```

---

## 八、ABAP 调用示例

### 8.1 查询风险规则

```abap
CLASS zcl_risk_group_matcher DEFINITION
  PUBLIC
  FINAL
  CREATE PUBLIC.

  PUBLIC SECTION.
    
    TYPES: BEGIN OF ty_filter_criteria,
             class         TYPE string,
             partsbrand    TYPE string,
             quality       TYPE string,
             carbrand      TYPE string,
             price         TYPE p LENGTH 15 DECIMALS 2,
             region        TYPE string,
             province      TYPE string,
             city          TYPE string,
             county        TYPE string,
             customer      TYPE string,
             store         TYPE string,
             cargrade      TYPE string,
           END OF ty_filter_criteria.
    
    CLASS-METHODS match_risk_groups
      IMPORTING
        is_criteria       TYPE ty_filter_criteria
      EXPORTING
        et_matched_groups TYPE STANDARD TABLE
        ev_has_risk       TYPE abap_bool.

ENDCLASS.

CLASS zcl_risk_group_matcher IMPLEMENTATION.

  METHOD match_risk_groups.
    
    DATA: lt_groups TYPE TABLE OF zc_risk_group.
    
    " 构建查询条件
    DATA(lv_price_range) = get_price_range_code( is_criteria-price ).
    
    " 使用EML查询
    READ ENTITIES OF zc_risk_group
      ENTITY riskgroup
        ALL FIELDS
        WITH VALUE #( )
      RESULT DATA(lt_all_groups).
    
    " 过滤匹配的规则
    LOOP AT lt_all_groups INTO DATA(ls_group).
      
      DATA(lv_match) = abap_true.
      
      " 品类匹配
      IF is_criteria-class IS NOT INITIAL.
        IF NOT contains( val = ls_group-ClassFilter sub = is_criteria-class ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      " 零件品牌匹配
      IF is_criteria-partsbrand IS NOT INITIAL.
        IF NOT contains( val = ls_group-PartsBrandFilter sub = is_criteria-partsbrand ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      " 品质匹配
      IF is_criteria-quality IS NOT INITIAL.
        IF NOT contains( val = ls_group-QualityFilter sub = is_criteria-quality ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      " 汽车品牌匹配
      IF is_criteria-carbrand IS NOT INITIAL.
        IF NOT contains( val = ls_group-CarBrandFilter sub = is_criteria-carbrand ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      " 价格区间匹配
      IF lv_price_range IS NOT INITIAL.
        IF NOT contains( val = ls_group-PriceFilter sub = lv_price_range ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      " 地区匹配
      IF is_criteria-province IS NOT INITIAL.
        IF NOT contains( val = ls_group-ZoneProvince sub = is_criteria-province ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      IF is_criteria-city IS NOT INITIAL.
        IF NOT contains( val = ls_group-ZoneCity sub = is_criteria-city ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      IF is_criteria-county IS NOT INITIAL.
        IF NOT contains( val = ls_group-ZoneCounty sub = is_criteria-county ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      " 连队匹配
      IF is_criteria-region IS NOT INITIAL.
        IF NOT contains( val = ls_group-RegionFilter sub = is_criteria-region ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      " 客户匹配
      IF is_criteria-customer IS NOT INITIAL.
        IF NOT contains( val = ls_group-CusFilter sub = is_criteria-customer ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      " 供应商匹配
      IF is_criteria-store IS NOT INITIAL.
        IF NOT contains( val = ls_group-StoreFilter sub = is_criteria-store ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      " 车辆等级匹配
      IF is_criteria-cargrade IS NOT INITIAL.
        IF NOT contains( val = ls_group-CarGradeFilter sub = is_criteria-cargrade ).
          CONTINUE.
        ENDIF.
      ENDIF.
      
      " 所有条件匹配，加入结果集
      APPEND ls_group TO et_matched_groups.
      
    ENDLOOP.
    
    " 判断是否有风险
    IF et_matched_groups IS NOT INITIAL.
      ev_has_risk = abap_true.
    ENDIF.
    
  ENDMETHOD.

ENDCLASS.
```

### 8.2 订单风险检查实际应用

```abap
" 在订单处理流程中调用
DATA: ls_criteria TYPE zcl_risk_group_matcher=>ty_filter_criteria,
      lt_matched  TYPE STANDARD TABLE OF zc_risk_group,
      lv_has_risk TYPE abap_bool.

" 从订单数据构建筛选条件
ls_criteria = VALUE #(
  class      = 'CAT001'
  partsbrand = 'BOSCH'
  quality    = 'OEM'
  carbrand   = 'BMW'
  price      = '2500.00'
  province   = '110000'
  city       = '110100'
  customer   = 'CUS001'
  store      = 'STORE001'
  cargrade   = 'HIGH'
).

" 执行匹配
zcl_risk_group_matcher=>match_risk_groups(
  EXPORTING
    is_criteria = ls_criteria
  IMPORTING
    et_matched_groups = lt_matched
    ev_has_risk = lv_has_risk
).

" 处理匹配结果
IF lv_has_risk = abap_true.
  
  LOOP AT lt_matched INTO DATA(ls_group).
    
    " 记录风险日志
    WRITE: / '检测到风险:', ls_group-RiskTitle.
    WRITE: / '风险内容:', ls_group-RiskContent.
    
    " 发送风险提醒到企业微信
    " (调用之前创建的企业微信接口)
    
  ENDLOOP.
  
ENDIF.
```

---

## 九、JavaScript 前端调用示例

### 9.1 封装 API 类

```javascript
/**
 * ZGROUP 风险组 OData API 封装
 */
class ZGroupRiskAPI {
  
  constructor(baseUrl) {
    this.baseUrl = baseUrl;
    this.servicePath = '/sap/opu/odata4/sap/zgroup_risk_o4/srvd/sap/zgroup_risk_srv/0001';
  }
  
  /**
   * 查询风险规则组
   * @param {Object} filters - 筛选条件对象
   * @returns {Promise<Array>} - 匹配的风险规则列表
   */
  async queryRiskGroups(filters) {
    
    const filterParts = [];
    
    // 品类筛选
    if (filters.class) {
      filterParts.push(`contains(ClassFilter,'${filters.class}')`);
    }
    
    // 零件品牌筛选
    if (filters.partsBrand) {
      filterParts.push(`contains(PartsBrandFilter,'${filters.partsBrand}')`);
    }
    
    // 品质筛选
    if (filters.quality) {
      filterParts.push(`contains(QualityFilter,'${filters.quality}')`);
    }
    
    // 汽车品牌筛选
    if (filters.carBrand) {
      filterParts.push(`contains(CarBrandFilter,'${filters.carBrand}')`);
    }
    
    // 价格区间筛选
    if (filters.priceRange) {
      filterParts.push(`contains(PriceFilter,'${filters.priceRange}')`);
    }
    
    // 连队筛选
    if (filters.region) {
      filterParts.push(`contains(RegionFilter,'${filters.region}')`);
    }
    
    // 省市区筛选
    if (filters.province) {
      filterParts.push(`contains(ZoneProvince,'${filters.province}')`);
    }
    if (filters.city) {
      filterParts.push(`contains(ZoneCity,'${filters.city}')`);
    }
    if (filters.county) {
      filterParts.push(`contains(ZoneCounty,'${filters.county}')`);
    }
    
    // 客户筛选
    if (filters.customer) {
      filterParts.push(`contains(CusFilter,'${filters.customer}')`);
    }
    
    // 供应商筛选
    if (filters.store) {
      filterParts.push(`contains(StoreFilter,'${filters.store}')`);
    }
    
    // 车辆等级筛选
    if (filters.carGrade) {
      filterParts.push(`contains(CarGradeFilter,'${filters.carGrade}')`);
    }
    
    // 只查询启用的规则
    filterParts.push(`Status eq 'A'`);
    
    // 构建完整URL
    const filterString = filterParts.join(' and ');
    const url = `${this.baseUrl}${this.servicePath}/RiskGroup` +
                `?$filter=${encodeURIComponent(filterString)}` +
                `&$expand=_Attachments` +
                `&$orderby=Priority desc`;
    
    try {
      const response = await fetch(url, {
        method: 'GET',
        headers: {
          'Accept': 'application/json',
          'Content-Type': 'application/json'
        },
        credentials: 'include'  // 携带认证信息
      });
      
      if (!response.ok) {
        throw new Error(`HTTP ${response.status}: ${response.statusText}`);
      }
      
      const data = await response.json();
      return data.value || [];
      
    } catch (error) {
      console.error('查询风险规则失败:', error);
      throw error;
    }
  }
  
  /**
   * 订单风险检查
   * @param {Object} orderData - 订单数据
   * @returns {Promise<Object>} - 风险检查结果
   */
  async checkOrderRisk(orderData) {
    
    const filters = {
      class: orderData.categoryCode,
      partsBrand: orderData.partsBrandId,
      quality: orderData.quality,
      carBrand: orderData.carBrandId,
      priceRange: this.getPriceRangeCode(orderData.price),
      province: orderData.provinceCode,
      city: orderData.cityCode,
      county: orderData.countyCode,
      region: orderData.regionId,
      customer: orderData.customerId,
      store: orderData.storeId,
      carGrade: orderData.carGrade
    };
    
    const riskGroups = await this.queryRiskGroups(filters);
    
    if (riskGroups && riskGroups.length > 0) {
      return {
        hasRisk: true,
        riskCount: riskGroups.length,
        risks: riskGroups.map(group => ({
          groupId: group.GroupId,
          groupName: group.GroupName,
          riskTitle: group.RiskTitle,
          riskContent: group.RiskContent,
          priority: group.Priority,
          attachments: group._Attachments || []
        }))
      };
    } else {
      return {
        hasRisk: false,
        riskCount: 0,
        risks: []
      };
    }
  }
  
  /**
   * 根据价格获取价格区间编码
   * @param {Number} price - 价格
   * @returns {String} - 价格区间编码
   */
  getPriceRangeCode(price) {
    if (price <= 100) return 'RANGE01';
    if (price <= 500) return 'RANGE02';
    if (price <= 1000) return 'RANGE03';
    if (price <= 2000) return 'RANGE04';
    if (price <= 3000) return 'RANGE05';
    return 'RANGE06';  // 3000以上
  }
  
  /**
   * 创建风险规则组
   * @param {Object} groupData - 规则组数据
   * @returns {Promise<Object>} - 创建结果
   */
  async createRiskGroup(groupData) {
    
    const url = `${this.baseUrl}${this.servicePath}/RiskGroup`;
    
    try {
      const response = await fetch(url, {
        method: 'POST',
        headers: {
          'Accept': 'application/json',
          'Content-Type': 'application/json'
        },
        credentials: 'include',
        body: JSON.stringify(groupData)
      });
      
      if (!response.ok) {
        const error = await response.json();
        throw new Error(error.error?.message || '创建失败');
      }
      
      return await response.json();
      
    } catch (error) {
      console.error('创建风险规则组失败:', error);
      throw error;
    }
  }
  
  /**
   * 更新风险规则组
   * @param {String} groupId - 规则组ID
   * @param {Object} updates - 更新数据
   * @returns {Promise<Object>} - 更新结果
   */
  async updateRiskGroup(groupId, updates) {
    
    const url = `${this.baseUrl}${this.servicePath}/RiskGroup(GroupId='${groupId}')`;
    
    try {
      const response = await fetch(url, {
        method: 'PATCH',
        headers: {
          'Accept': 'application/json',
          'Content-Type': 'application/json'
        },
        credentials: 'include',
        body: JSON.stringify(updates)
      });
      
      if (!response.ok) {
        const error = await response.json();
        throw new Error(error.error?.message || '更新失败');
      }
      
      return await response.json();
      
    } catch (error) {
      console.error('更新风险规则组失败:', error);
      throw error;
    }
  }
}

// ============================================================================
// 使用示例
// ============================================================================

// 初始化API
const riskAPI = new ZGroupRiskAPI('https://your-sap-server.com');

// 示例1: 订单提交前检查风险
async function checkBeforeSubmit() {
  
  const orderData = {
    categoryCode: 'CAT001',
    partsBrandId: 'BOSCH',
    quality: 'OEM',
    carBrandId: 'BMW',
    price: 2500,
    provinceCode: '110000',
    cityCode: '110100',
    countyCode: '110101',
    customerId: 'CUS001',
    storeId: 'STORE001',
    carGrade: 'HIGH'
  };
  
  try {
    const result = await riskAPI.checkOrderRisk(orderData);
    
    if (result.hasRisk) {
      console.log(`检测到 ${result.riskCount} 个风险提示`);
      
      // 显示风险警告对话框
      showRiskWarningDialog(result.risks);
      
      // 等待用户确认
      const userConfirmed = await getUserConfirmation();
      
      if (userConfirmed) {
        // 用户确认后继续提交
        submitOrder(orderData);
      }
      
    } else {
      console.log('未检测到风险,可以继续提交');
      submitOrder(orderData);
    }
    
  } catch (error) {
    console.error('风险检查失败:', error);
    alert('风险检查服务暂时不可用,请稍后重试');
  }
}

// 示例2: 显示风险警告对话框
function showRiskWarningDialog(risks) {
  
  const riskHtml = risks.map(risk => `
    <div class="risk-item priority-${risk.priority}">
      <h3>${risk.riskTitle}</h3>
      <p>${risk.riskContent}</p>
      ${risk.attachments.length > 0 ? `
        <div class="attachments">
          ${risk.attachments.map(att => `
            <a href="${att.FileUrl}" target="_blank">
              ${att.FileName} (${att.FileTypeText})
            </a>
          `).join('')}
        </div>
      ` : ''}
    </div>
  `).join('');
  
  // 显示模态框
  const dialog = document.getElementById('risk-warning-dialog');
  dialog.querySelector('.risk-list').innerHTML = riskHtml;
  dialog.style.display = 'block';
}

// 示例3: 管理界面 - 创建新规则
async function createNewRiskRule() {
  
  const newRule = {
    GroupCode: 'RISK001',
    GroupName: '高价值零件风险提示',
    GroupDesc: '针对高价值零件的风险警告规则',
    ClassFilter: 'CAT001,CAT002',
    PriceFilter: 'RANGE05,RANGE06',
    RiskTitle: '高价值零件风险',
    RiskContent: '该零件价格较高，请仔细核对规格和质量标准',
    Status: 'A',
    Priority: 100,
    ValidFrom: '2025-01-01',
    ValidTo: '9999-12-31'
  };
  
  try {
    const result = await riskAPI.createRiskGroup(newRule);
    console.log('规则创建成功:', result);
    alert('风险规则创建成功!');
    
    // 刷新列表
    loadRiskRuleList();
    
  } catch (error) {
    console.error('创建失败:', error);
    alert('创建失败: ' + error.message);
  }
}
```

---

## 十、价格区间配置

### 10.1 预设价格区间定义

| 区间编码 | 区间描述 | 最小值 | 最大值 | 显示顺序 |
|---------|---------|-------|-------|---------|
| RANGE01 | [1-100] | 1.00 | 100.00 | 10 |
| RANGE02 | [101-500] | 101.00 | 500.00 | 20 |
| RANGE03 | [501-1000] | 501.00 | 1000.00 | 30 |
| RANGE04 | [1001-2000] | 1001.00 | 2000.00 | 40 |
| RANGE05 | [2001-3000] | 2001.00 | 3000.00 | 50 |
| RANGE06 | [3000以上] | 3000.00 | 999999.99 | 60 |

### 10.2 价格区间配置表 (可选)

```abap
@EndUserText.label : '价格区间配置表'
define table ztint_price_range_cfg {
  
  key client          : abap.clnt not null;
  key range_code      : abap.char(10) not null;
  
  range_desc          : abap.char(50);
  min_price           : abap.dec(15,2);
  max_price           : abap.dec(15,2);
  display_order       : abap.int2;
  is_active           : abap.char(1);
  
}
```

---

## 十一、测试数据示例

### 11.1 示例规则1: 高价值发动机风险

```sql
INSERT INTO ztint_risk_group VALUES (
  group_id: 'A1B2C3D4E5F6G7H8I9J0K1L2M3N4O5P6',
  group_code: 'RISK_ENGINE_001',
  group_name: '高价值发动机风险提示',
  group_desc: '当订单涉及高价值发动机时触发风险提示',
  class_filter: 'CAT_ENGINE_01,CAT_ENGINE_02',
  partsbrand_filter: '',
  quality_filter: '',
  carbrand_filter: '',
  price_filter: 'RANGE05,RANGE06',
  region_filter: '',
  zone_province: '',
  zone_city: '',
  zone_county: '',
  cus_filter: '',
  store_filter: '',
  cargrade_filter: 'HIGH',
  risk_title: '高价值发动机风险',
  risk_content: '该发动机价格较高，建议:\n1. 仔细核对发动机型号\n2. 确认质量标准\n3. 检查供应商资质',
  status: 'A',
  priority: 100,
  valid_from: '20250101',
  valid_to: '99991231',
  created_by: 'ADMIN',
  created_at: '20250103',
  created_time: '120000'
);
```

### 11.2 示例规则2: 区域限制风险

```sql
INSERT INTO ztint_risk_group VALUES (
  group_id: 'B2C3D4E5F6G7H8I9J0K1L2M3N4O5P6Q7',
  group_code: 'RISK_REGION_001',
  group_name: '深圳地区特定品牌风险',
  group_desc: '深圳地区特定品牌零件需要额外审核',
  class_filter: '',
  partsbrand_filter: 'BRAND_A,BRAND_B',
  quality_filter: '',
  carbrand_filter: '',
  price_filter: '',
  region_filter: '',
  zone_province: '440000',
  zone_city: '440300',
  zone_county: '',
  cus_filter: '',
  store_filter: '',
  cargrade_filter: '',
  risk_title: '深圳地区品牌限制',
  risk_content: '该品牌在深圳地区需要额外审核，请联系区域经理',
  status: 'A',
  priority: 90,
  valid_from: '20250101',
  valid_to: '20251231',
  created_by: 'ADMIN',
  created_at: '20250103',
  created_time: '120000'
);
```

---

## 十二、部署清单

### 数据库对象
- [ ] ZTINT_RISK_GROUP (主表)
- [ ] ZTINT_RISK_ATTACH (附件表)
- [ ] ZTINT_PRICE_RANGE_CFG (价格区间配置表 - 可选)

### CDS Views
- [ ] ZI_RISK_GROUP (接口视图)
- [ ] ZI_RISK_ATTACH (附件接口视图)
- [ ] ZC_RISK_GROUP (消费视图)
- [ ] ZC_RISK_ATTACH (附件消费视图)

### 行为定义
- [ ] ZI_RISK_GROUP (接口层行为)
- [ ] ZC_RISK_GROUP (投影层行为)

### 服务
- [ ] ZGROUP_RISK_SRV (Service Definition)
- [ ] ZGROUP_RISK_O4 (Service Binding)

### ABAP 类
- [ ] ZBP_I_RISK_GROUP (行为实现类)
- [ ] ZCL_RISK_GROUP_MATCHER (匹配工具类)

### 测试
- [ ] 单元测试
- [ ] 集成测试
- [ ] 性能测试

---

## 十三、注意事项与最佳实践

### 13.1 性能优化

1. **索引策略**
   - 在 `status`, `valid_from`, `valid_to` 上创建索引
   - 对常用查询字段创建组合索引

2. **缓存机制**
   - 对不常变化的规则启用缓存
   - 设置合理的缓存失效时间 (建议10-30分钟)

3. **查询优化**
   - 避免使用 `$select *`,只选择需要的字段
   - 合理使用 `$top` 和 `$skip` 进行分页
   - 使用 `$expand` 时注意性能影响

### 13.2 安全性考虑

1. **输入验证**
   - 所有输入参数进行严格验证
   - 防止 SQL 注入和 XSS 攻击

2. **权限控制**
   - 实现基于角色的访问控制 (RBAC)
   - 敏感数据字段脱敏处理

3. **审计日志**
   - 记录所有 CRUD 操作
   - 记录规则匹配日志

### 13.3 扩展性设计

1. **规则引擎扩展**
   - 支持更复杂的逻辑表达式 (AND/OR/NOT)
   - 支持规则优先级和冲突解决

2. **通知机制**
   - 集成企业微信/钉钉通知
   - 支持邮件/短信提醒

3. **数据分析**
   - 风险命中率统计
   - 规则有效性分析
   - 业务趋势分析

---

## 十四、常见问题 (FAQ)

### Q1: 如何处理多选条件的 OR 逻辑?
A: 在数据库中使用逗号分隔存储多个值，查询时使用 `contains()` 函数进行匹配。

### Q2: 价格区间如何动态配置?
A: 可以使用独立的配置表 `ZTINT_PRICE_RANGE_CFG` 进行管理，支持动态新增和修改。

### Q3: 如何实现区域的层级联动?
A: 使用省市区三个独立字段存储，查询时按层级依次匹配，上级为空表示不限制。

### Q4: 规则优先级如何处理?
A: 使用 `Priority` 字段进行排序，数值越大优先级越高，匹配时按优先级返回结果。

### Q5: 如何测试 OData 接口?
A: 推荐使用以下工具:
- SAP Gateway Client (事务码: /IWFND/GW_CLIENT)
- Postman
- Chrome 浏览器直接访问

---

## 十五、技术文档参考

1. [SAP ABAP RESTful Application Programming Model (RAP)](https://help.sap.com/docs/ABAP_PLATFORM_NEW/923180ddb98240829d935862025004d6/289477a81eec4d4e84c0302fb6835035.html)
2. [OData V4 Specification](https://www.odata.org/documentation/)
3. [CDS View Development Guide](https://help.sap.com/docs/ABAP_PLATFORM_NEW/cc0c305d2fab47bd808adcad3ca7ee9d/3a9d40c03ff5472dbde8f6f1e1e4bed4.html)
4. [SAP Fiori Elements](https://sapui5.hana.ondemand.com/sdk/#/topic/03265b0408e2432c9571d6b3feb6b1fd)

---

**文档版本**: v1.0  
**创建日期**: 2025-11-03  
**作者**: AI Assistant  
**审核状态**: 待审核  
**适用版本**: SAP S/4HANA 1909+


