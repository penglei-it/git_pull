# 风险提示配置 OData 接口测试文档

## 一、测试环境配置

### 1.1 基础信息

```yaml
服务器地址: https://your-sap-server.com:port
服务路径: /sap/opu/odata/sap/ZUI_RISK_RULE_SRV
实体集名称: RiskRuleSet
认证方式: Basic Auth / OAuth 2.0
```

### 1.2 测试工具

推荐使用以下工具之一:
- **Postman** - 最流行的API测试工具
- **SAP Gateway Client** (Transaction: /IWFND/GW_CLIENT)
- **cURL** - 命令行工具
- **Insomnia** - REST客户端

---

## 二、测试用例集

### 测试用例 1: 获取所有启用的规则

**目的**: 验证基础查询功能

**请求**:
```http
GET /sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet?$filter=Status eq 'A'
Accept: application/json
Authorization: Basic <base64_credentials>
```

**预期响应** (HTTP 200):
```json
{
  "d": {
    "results": [
      {
        "__metadata": {
          "id": "https://server/sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet('GUID1')",
          "uri": "https://server/sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet('GUID1')",
          "type": "ZUI_RISK_RULE_SRV.RiskRule"
        },
        "RuleId": "GUID1",
        "RuleName": "高风险零件提示",
        "Status": "A",
        "StatusText": "启用",
        "Priority": 100,
        "RiskTitle": "高价值零件风险",
        "RiskContent": "该零件价格较高，请谨慎核对"
      }
    ]
  }
}
```

**测试断言**:
- ✅ 状态码 = 200
- ✅ 所有返回记录的 Status = 'A'
- ✅ 返回数据结构正确

---

### 测试用例 2: 按品类筛选 (单选)

**目的**: 验证单一条件筛选

**请求**:
```http
GET /sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet
  ?$filter=ClassA eq 'CAT001' and Status eq 'A'
  &$format=json
```

**预期响应** (HTTP 200):
```json
{
  "d": {
    "results": [
      {
        "RuleId": "GUID2",
        "RuleName": "一级分类风险提示",
        "ClassA": "CAT001,CAT002",
        "Status": "A"
      }
    ]
  }
}
```

**测试断言**:
- ✅ 所有返回记录的 ClassA 包含 'CAT001'
- ✅ 数据条数 > 0

---

### 测试用例 3: 多条件组合查询

**目的**: 验证多个筛选条件的 AND 组合

**请求**:
```http
GET /sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet
  ?$filter=ClassA eq 'CAT001' 
    and PartsBrand eq 'BRAND001' 
    and Quality eq 'OEM'
    and Status eq 'A'
  &$orderby=Priority desc
  &$format=json
```

**预期响应** (HTTP 200):
返回同时满足品类、品牌、品质三个条件的规则

**测试断言**:
- ✅ ClassA 包含 'CAT001'
- ✅ PartsBrand 包含 'BRAND001'
- ✅ Quality 包含 'OEM'
- ✅ 结果按 Priority 降序排列

---

### 测试用例 4: 多选查询 (IN 操作符)

**目的**: 验证多选条件查询

**请求**:
```http
GET /sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet
  ?$filter=(ClassA eq 'CAT001' or ClassA eq 'CAT002' or ClassA eq 'CAT003')
    and Status eq 'A'
  &$format=json
```

或使用简化写法 (OData V4):
```http
GET /sap/opu/odata4/sap/ZUI_RISK_RULE_O4/RiskRule
  ?$filter=ClassA in ('CAT001','CAT002','CAT003') and Status eq 'A'
```

**预期响应** (HTTP 200):
返回品类为 CAT001、CAT002 或 CAT003 的规则

**测试断言**:
- ✅ 返回记录的 ClassA 包含任一指定值
- ✅ 多选逻辑正确 (OR 关系)

---

### 测试用例 5: 价格区间查询

**目的**: 验证价格区间筛选

**测试数据**:
- 价格 1500 元 → 应匹配 RANGE04 [1001-2000]

**请求**:
```http
GET /sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet
  ?$filter=PriceRange eq 'RANGE04' and Status eq 'A'
  &$format=json
```

**预期响应** (HTTP 200):
返回价格区间包含 RANGE04 的规则

**测试断言**:
- ✅ PriceRange 字段包含 'RANGE04'
- ✅ 价格区间匹配逻辑正确

---

### 测试用例 6: 省市区联动查询

**目的**: 验证地域筛选的级联关系

**请求**:
```http
GET /sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet
  ?$filter=ProvinceCode eq '110000' 
    and CityCode eq '110100'
    and CountyCode eq '110101'
    and Status eq 'A'
  &$format=json
```

**预期响应** (HTTP 200):
返回北京市东城区的风险规则

**测试断言**:
- ✅ 省市区编码匹配
- ✅ 级联查询逻辑正确

---

### 测试用例 7: 包含附件的查询 ($expand)

**目的**: 验证关联实体展开

**请求**:
```http
GET /sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet('GUID1')
  ?$expand=_Files
  &$format=json
```

**预期响应** (HTTP 200):
```json
{
  "d": {
    "RuleId": "GUID1",
    "RuleName": "高风险零件提示",
    "_Files": {
      "results": [
        {
          "RuleId": "GUID1",
          "FileId": "FILE001",
          "FileName": "风险说明.pdf",
          "FileType": "PDF",
          "FileUrl": "https://cdn.example.com/files/risk001.pdf"
        }
      ]
    }
  }
}
```

**测试断言**:
- ✅ _Files 节点存在
- ✅ 附件信息完整

---

### 测试用例 8: 分页查询

**目的**: 验证分页功能

**请求**:
```http
GET /sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet
  ?$filter=Status eq 'A'
  &$top=10
  &$skip=0
  &$inlinecount=allpages
  &$format=json
```

**预期响应** (HTTP 200):
```json
{
  "d": {
    "__count": "125",
    "results": [
      // 10条记录
    ]
  }
}
```

**测试断言**:
- ✅ 返回记录数 = 10
- ✅ __count 字段存在且 > 0
- ✅ 分页参数生效

---

### 测试用例 9: 复杂组合查询

**目的**: 验证真实业务场景

**业务场景**: 查询北京地区、价格 1000-3000、品质为 OEM 的风险规则

**请求**:
```http
GET /sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet
  ?$filter=(PriceRange eq 'RANGE04' or PriceRange eq 'RANGE05')
    and Quality eq 'OEM'
    and ProvinceCode eq '110000'
    and Status eq 'A'
  &$expand=_Files
  &$orderby=Priority desc
  &$top=20
  &$format=json
```

**预期响应** (HTTP 200):
返回符合所有条件的规则,包含附件信息

**测试断言**:
- ✅ 所有过滤条件生效
- ✅ 排序正确
- ✅ 附件数据展开
- ✅ 分页限制生效

---

### 测试用例 10: 全文搜索 ($search)

**目的**: 验证全文搜索功能 (如果支持)

**请求**:
```http
GET /sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet
  ?$search=高风险
  &$format=json
```

**预期响应** (HTTP 200):
返回规则名称或描述中包含"高风险"的记录

**测试断言**:
- ✅ 返回结果包含关键词
- ✅ 模糊搜索生效

---

## 三、性能测试用例

### 测试用例 P1: 并发查询测试

**目的**: 验证并发处理能力

**测试配置**:
- 并发用户数: 100
- 每用户请求数: 10
- 总请求数: 1000

**性能指标**:
- ✅ 平均响应时间 < 2秒
- ✅ 95% 响应时间 < 3秒
- ✅ 错误率 < 1%

**JMeter 配置示例**:
```xml
<ThreadGroup>
  <stringProp name="ThreadGroup.num_threads">100</stringProp>
  <stringProp name="ThreadGroup.ramp_time">10</stringProp>
  <stringProp name="LoopController.loops">10</stringProp>
</ThreadGroup>
```

---

### 测试用例 P2: 大数据量测试

**目的**: 验证大数据量下的性能

**测试数据**:
- 规则总数: 10,000+
- 附件总数: 50,000+

**请求**:
```http
GET /sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet
  ?$filter=Status eq 'A'
  &$top=1000
  &$format=json
```

**性能指标**:
- ✅ 查询响应时间 < 5秒
- ✅ 内存使用合理
- ✅ 无超时错误

---

## 四、异常场景测试

### 测试用例 E1: 无效参数

**请求**:
```http
GET /sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet
  ?$filter=InvalidField eq 'test'
```

**预期响应** (HTTP 400):
```json
{
  "error": {
    "code": "400",
    "message": {
      "lang": "en",
      "value": "Invalid filter field: InvalidField"
    }
  }
}
```

**测试断言**:
- ✅ 状态码 = 400
- ✅ 错误信息清晰

---

### 测试用例 E2: 认证失败

**请求**:
```http
GET /sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet
Authorization: Basic invalid_credentials
```

**预期响应** (HTTP 401):
```json
{
  "error": {
    "code": "401",
    "message": {
      "value": "Unauthorized"
    }
  }
}
```

**测试断言**:
- ✅ 状态码 = 401
- ✅ 未授权访问被拦截

---

### 测试用例 E3: 资源不存在

**请求**:
```http
GET /sap/opu/odata/sap/ZUI_RISK_RULE_SRV/RiskRuleSet('INVALID_GUID')
```

**预期响应** (HTTP 404):
```json
{
  "error": {
    "code": "404",
    "message": {
      "value": "Resource not found"
    }
  }
}
```

**测试断言**:
- ✅ 状态码 = 404
- ✅ 资源不存在提示

---

## 五、自动化测试脚本

### 5.1 Postman Collection 示例

```json
{
  "info": {
    "name": "Risk Rule API Tests",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "item": [
    {
      "name": "获取所有启用规则",
      "request": {
        "method": "GET",
        "header": [],
        "url": {
          "raw": "{{baseUrl}}/RiskRuleSet?$filter=Status eq 'A'&$format=json",
          "host": ["{{baseUrl}}"],
          "path": ["RiskRuleSet"],
          "query": [
            {
              "key": "$filter",
              "value": "Status eq 'A'"
            },
            {
              "key": "$format",
              "value": "json"
            }
          ]
        }
      },
      "event": [
        {
          "listen": "test",
          "script": {
            "exec": [
              "pm.test('Status code is 200', function() {",
              "  pm.response.to.have.status(200);",
              "});",
              "",
              "pm.test('Response has results', function() {",
              "  var jsonData = pm.response.json();",
              "  pm.expect(jsonData.d.results).to.be.an('array');",
              "  pm.expect(jsonData.d.results.length).to.be.above(0);",
              "});",
              "",
              "pm.test('All rules are active', function() {",
              "  var jsonData = pm.response.json();",
              "  jsonData.d.results.forEach(function(rule) {",
              "    pm.expect(rule.Status).to.equal('A');",
              "  });",
              "});"
            ]
          }
        }
      ]
    }
  ],
  "variable": [
    {
      "key": "baseUrl",
      "value": "https://your-server/sap/opu/odata/sap/ZUI_RISK_RULE_SRV"
    }
  ]
}
```

### 5.2 cURL 批量测试脚本

```bash
#!/bin/bash

# 配置
BASE_URL="https://your-server/sap/opu/odata/sap/ZUI_RISK_RULE_SRV"
USERNAME="your_username"
PASSWORD="your_password"
AUTH=$(echo -n "$USERNAME:$PASSWORD" | base64)

echo "========================================="
echo "风险规则 API 测试"
echo "========================================="

# 测试1: 基础查询
echo -e "\n[测试1] 获取所有启用规则..."
response=$(curl -s -w "\n%{http_code}" \
  -H "Authorization: Basic $AUTH" \
  -H "Accept: application/json" \
  "$BASE_URL/RiskRuleSet?\$filter=Status%20eq%20'A'&\$format=json")
  
http_code=$(echo "$response" | tail -n1)
if [ "$http_code" == "200" ]; then
  echo "✅ 测试通过 (HTTP $http_code)"
else
  echo "❌ 测试失败 (HTTP $http_code)"
fi

# 测试2: 品类筛选
echo -e "\n[测试2] 按品类筛选..."
response=$(curl -s -w "\n%{http_code}" \
  -H "Authorization: Basic $AUTH" \
  -H "Accept: application/json" \
  "$BASE_URL/RiskRuleSet?\$filter=ClassA%20eq%20'CAT001'%20and%20Status%20eq%20'A'&\$format=json")
  
http_code=$(echo "$response" | tail -n1)
if [ "$http_code" == "200" ]; then
  echo "✅ 测试通过 (HTTP $http_code)"
else
  echo "❌ 测试失败 (HTTP $http_code)"
fi

# 测试3: 复杂组合查询
echo -e "\n[测试3] 复杂组合查询..."
response=$(curl -s -w "\n%{http_code}" \
  -H "Authorization: Basic $AUTH" \
  -H "Accept: application/json" \
  "$BASE_URL/RiskRuleSet?\$filter=(PriceRange%20eq%20'RANGE04'%20or%20PriceRange%20eq%20'RANGE05')%20and%20Quality%20eq%20'OEM'&\$format=json")
  
http_code=$(echo "$response" | tail -n1)
if [ "$http_code" == "200" ]; then
  echo "✅ 测试通过 (HTTP $http_code)"
else
  echo "❌ 测试失败 (HTTP $http_code)"
fi

echo -e "\n========================================="
echo "测试完成"
echo "========================================="
```

---

## 六、测试数据准备

### 6.1 测试规则数据

```sql
-- 插入测试规则
INSERT INTO ztint_risk_rule VALUES (
  '001', 'RULE0001', '高价零件风险提示', '针对高价值零件的风险提示',
  'A', 100, '高价值零件风险', '该零件价格较高，请谨慎核对规格和质量',
  'CAT001,CAT002', '', '', '', 'BRAND001,BRAND002', 'OEM',
  'BMW,BENZ', 'RANGE05,RANGE06', '', '110000', '110100', '110101', '',
  '', '', '', '', '20250101', '99991231',
  '20250103', '120000', 'TEST_USER', '20250103', '120000', 'TEST_USER'
);

-- 插入附件数据
INSERT INTO ztint_risk_file VALUES (
  '001', 'RULE0001', 'FILE0001', '风险说明.pdf', 'PDF',
  'https://cdn.example.com/files/risk001.pdf', 1024000,
  '20250103', '120000', 'TEST_USER'
);
```

---

## 七、测试报告模板

### 测试执行摘要

| 测试日期 | 2025-11-03 |
|---------|-----------|
| 测试环境 | DEV/QA/PROD |
| 测试人员 | [姓名] |
| 测试工具 | Postman / JMeter |

### 测试结果统计

| 类别 | 总数 | 通过 | 失败 | 通过率 |
|------|------|------|------|--------|
| 功能测试 | 10 | 10 | 0 | 100% |
| 性能测试 | 2 | 2 | 0 | 100% |
| 异常测试 | 3 | 3 | 0 | 100% |
| **合计** | **15** | **15** | **0** | **100%** |

### 缺陷清单

| 编号 | 优先级 | 描述 | 状态 |
|------|--------|------|------|
| BUG001 | 高 | 多选查询返回数据不正确 | 已修复 |
| BUG002 | 中 | 分页查询 __count 缺失 | 处理中 |

---

## 八、常见问题 FAQ

### Q1: 如何测试多选条件?

**A**: 使用 `or` 操作符或 `in` 操作符(OData V4):

```http
# OData V2
$filter=(ClassA eq 'CAT001' or ClassA eq 'CAT002')

# OData V4
$filter=ClassA in ('CAT001','CAT002')
```

### Q2: 如何处理 CSRF Token?

**A**: 对于修改操作(POST/PUT/DELETE),需要先获取 token:

```bash
# 1. 获取 Token
curl -X GET "$BASE_URL/RiskRuleSet" \
  -H "X-CSRF-Token: Fetch" \
  -c cookies.txt \
  -D headers.txt

# 2. 提取 Token
TOKEN=$(grep -i "x-csrf-token" headers.txt | cut -d' ' -f2)

# 3. 使用 Token
curl -X POST "$BASE_URL/RiskRuleSet" \
  -H "X-CSRF-Token: $TOKEN" \
  -b cookies.txt \
  -d @data.json
```

### Q3: 如何调试 OData 查询?

**A**: 使用 SAP Gateway Client (T-Code: /IWFND/GW_CLIENT):
1. 输入服务路径
2. 执行查询
3. 查看请求/响应详情
4. 检查日志

---

**测试文档版本**: v1.0  
**最后更新**: 2025-11-03

