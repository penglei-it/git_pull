# 风险提示配置 - 前端 UI 集成指南

## 一、Vue 3 + Element Plus 实现

### 1.1 风险筛选组件 (RiskFilterPanel.vue)

```vue
<template>
  <div class="risk-filter-panel">
    <el-form :model="filterForm" label-width="120px">
      <el-row :gutter="20">
        
        <!-- 品类筛选 -->
        <el-col :span="12">
          <el-form-item label="品类">
            <el-cascader
              v-model="filterForm.classList"
              :options="classOptions"
              :props="{ multiple: true, checkStrictly: true }"
              placeholder="请选择品类(支持多选)"
              clearable
            />
          </el-form-item>
        </el-col>
        
        <!-- 零件品牌 -->
        <el-col :span="12">
          <el-form-item label="零件品牌">
            <el-select
              v-model="filterForm.partsBrandList"
              multiple
              filterable
              placeholder="请选择零件品牌(支持多选)"
            >
              <el-option
                v-for="item in brandOptions"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
          </el-form-item>
        </el-col>
        
        <!-- 品质 -->
        <el-col :span="12">
          <el-form-item label="品质">
            <el-select
              v-model="filterForm.qualityList"
              multiple
              placeholder="请选择品质(支持多选)"
            >
              <el-option label="原厂件(OEM)" value="OEM" />
              <el-option label="品牌件" value="BRAND" />
              <el-option label="拆车件" value="USED" />
              <el-option label="副厂件" value="AFTERMARKET" />
            </el-select>
          </el-form-item>
        </el-col>
        
        <!-- 汽车品牌 -->
        <el-col :span="12">
          <el-form-item label="汽车品牌">
            <el-select
              v-model="filterForm.carBrandList"
              multiple
              filterable
              placeholder="请选择汽车品牌(支持多选)"
            >
              <el-option
                v-for="item in carBrandOptions"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
          </el-form-item>
        </el-col>
        
        <!-- 价格区间 -->
        <el-col :span="12">
          <el-form-item label="价格区间">
            <el-checkbox-group v-model="filterForm.priceRangeList">
              <el-checkbox label="RANGE01">1-100元</el-checkbox>
              <el-checkbox label="RANGE02">101-500元</el-checkbox>
              <el-checkbox label="RANGE03">501-1000元</el-checkbox>
              <el-checkbox label="RANGE04">1001-2000元</el-checkbox>
              <el-checkbox label="RANGE05">2001-3000元</el-checkbox>
              <el-checkbox label="RANGE06">3000元以上</el-checkbox>
            </el-checkbox-group>
          </el-form-item>
        </el-col>
        
        <!-- 区域选择 - 切换模式 -->
        <el-col :span="24">
          <el-form-item label="区域筛选">
            <el-radio-group v-model="regionMode" @change="handleRegionModeChange">
              <el-radio label="region">按连队</el-radio>
              <el-radio label="zone">按省市区</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
        
        <!-- 连队选择 -->
        <el-col v-if="regionMode === 'region'" :span="12">
          <el-form-item label="连队">
            <el-select
              v-model="filterForm.regionList"
              multiple
              filterable
              placeholder="请选择连队(支持多选)"
            >
              <el-option
                v-for="item in regionOptions"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
          </el-form-item>
        </el-col>
        
        <!-- 省市区选择 -->
        <el-col v-if="regionMode === 'zone'" :span="12">
          <el-form-item label="省市区">
            <el-cascader
              v-model="filterForm.zoneList"
              :options="zoneOptions"
              :props="{ 
                multiple: true, 
                checkStrictly: true,
                value: 'code',
                label: 'name'
              }"
              placeholder="请选择省市区(支持多选)"
              clearable
            />
          </el-form-item>
        </el-col>
        
        <!-- 维修厂 -->
        <el-col :span="12">
          <el-form-item label="维修厂">
            <el-select
              v-model="filterForm.customerList"
              multiple
              filterable
              remote
              :remote-method="searchCustomer"
              placeholder="请输入维修厂名称搜索"
            >
              <el-option
                v-for="item in customerOptions"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
          </el-form-item>
        </el-col>
        
        <!-- 供应商 -->
        <el-col :span="12">
          <el-form-item label="供应商">
            <el-select
              v-model="filterForm.storeList"
              multiple
              filterable
              remote
              :remote-method="searchStore"
              placeholder="请输入供应商名称搜索"
            >
              <el-option
                v-for="item in storeOptions"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
          </el-form-item>
        </el-col>
        
        <!-- 车辆等级 -->
        <el-col :span="12">
          <el-form-item label="车辆等级">
            <el-select
              v-model="filterForm.carGradeList"
              multiple
              placeholder="请选择车辆等级(支持多选)"
            >
              <el-option label="高端车" value="HIGH" />
              <el-option label="中端车" value="MID" />
              <el-option label="经济型车" value="LOW" />
            </el-select>
          </el-form-item>
        </el-col>
        
        <!-- 是否严选件 -->
        <el-col :span="12">
          <el-form-item label="是否严选件">
            <el-radio-group v-model="filterForm.isStrict">
              <el-radio label="">全部</el-radio>
              <el-radio label="X">是</el-radio>
              <el-radio label="">否</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
        
      </el-row>
      
      <!-- 操作按钮 -->
      <el-row>
        <el-col :span="24" style="text-align: right;">
          <el-button @click="handleReset">重置</el-button>
          <el-button type="primary" @click="handleQuery" :loading="loading">
            查询
          </el-button>
        </el-col>
      </el-row>
    </el-form>
    
    <!-- 查询结果 -->
    <div v-if="riskRules.length > 0" class="risk-results">
      <el-alert
        title="检测到风险提示"
        type="warning"
        :closable="false"
        show-icon
      >
        <template #default>
          <div>共检测到 <strong>{{ riskRules.length }}</strong> 条风险提示</div>
        </template>
      </el-alert>
      
      <div class="risk-list">
        <el-card
          v-for="rule in riskRules"
          :key="rule.RuleId"
          class="risk-card"
          shadow="hover"
        >
          <template #header>
            <div class="card-header">
              <span class="risk-title">
                <el-icon color="#e6a23c"><Warning /></el-icon>
                {{ rule.RiskTitle }}
              </span>
              <el-tag :type="getPriorityType(rule.Priority)">
                优先级: {{ rule.Priority }}
              </el-tag>
            </div>
          </template>
          
          <div class="risk-content">
            <p>{{ rule.RiskContent }}</p>
            
            <!-- 附件展示 -->
            <div v-if="rule._Files && rule._Files.results.length > 0" class="risk-files">
              <el-divider content-position="left">相关附件</el-divider>
              <div class="file-list">
                <el-link
                  v-for="file in rule._Files.results"
                  :key="file.FileId"
                  :href="file.FileUrl"
                  target="_blank"
                  :icon="getFileIcon(file.FileType)"
                >
                  {{ file.FileName }}
                </el-link>
              </div>
            </div>
          </div>
        </el-card>
      </div>
    </div>
    
    <el-empty v-else-if="!loading && hasQueried" description="未检测到风险" />
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
import { ElMessage } from 'element-plus'
import { Warning, Document, Picture, VideoCamera } from '@element-plus/icons-vue'
import { queryRiskRules } from '@/api/risk'

// 表单数据
const filterForm = reactive({
  classList: [],
  partsBrandList: [],
  qualityList: [],
  carBrandList: [],
  priceRangeList: [],
  regionList: [],
  zoneList: [],
  customerList: [],
  storeList: [],
  carGradeList: [],
  isStrict: ''
})

// 状态
const loading = ref(false)
const hasQueried = ref(false)
const riskRules = ref([])
const regionMode = ref('zone') // 'region' | 'zone'

// 选项数据 (实际应从API获取)
const classOptions = ref([
  {
    value: 'CAT001',
    label: '发动机系统',
    children: [
      { value: 'CAT001-01', label: '发动机总成' },
      { value: 'CAT001-02', label: '发动机配件' }
    ]
  },
  {
    value: 'CAT002',
    label: '底盘系统',
    children: [
      { value: 'CAT002-01', label: '变速箱' },
      { value: 'CAT002-02', label: '传动系统' }
    ]
  }
])

const brandOptions = ref([
  { value: 'BRAND001', label: '博世(BOSCH)' },
  { value: 'BRAND002', label: '德尔福(DELPHI)' },
  { value: 'BRAND003', label: '马勒(MAHLE)' }
])

const carBrandOptions = ref([
  { value: 'BMW', label: '宝马(BMW)' },
  { value: 'BENZ', label: '奔驰(BENZ)' },
  { value: 'AUDI', label: '奥迪(AUDI)' }
])

const regionOptions = ref([
  { value: 'REGION01', label: '华北连队' },
  { value: 'REGION02', label: '华东连队' },
  { value: 'REGION03', label: '华南连队' }
])

const zoneOptions = ref([
  {
    code: '110000',
    name: '北京市',
    children: [
      {
        code: '110100',
        name: '市辖区',
        children: [
          { code: '110101', name: '东城区' },
          { code: '110102', name: '西城区' }
        ]
      }
    ]
  },
  {
    code: '310000',
    name: '上海市',
    children: [
      {
        code: '310100',
        name: '市辖区',
        children: [
          { code: '310101', name: '黄浦区' },
          { code: '310104', name: '徐汇区' }
        ]
      }
    ]
  }
])

const customerOptions = ref([])
const storeOptions = ref([])

// 区域模式切换
const handleRegionModeChange = (mode) => {
  if (mode === 'region') {
    filterForm.zoneList = []
  } else {
    filterForm.regionList = []
  }
}

// 远程搜索维修厂
const searchCustomer = async (query) => {
  if (query) {
    // 实际应调用API搜索
    customerOptions.value = [
      { value: 'CUS001', label: `维修厂A - ${query}` },
      { value: 'CUS002', label: `维修厂B - ${query}` }
    ]
  }
}

// 远程搜索供应商
const searchStore = async (query) => {
  if (query) {
    // 实际应调用API搜索
    storeOptions.value = [
      { value: 'STORE001', label: `供应商A - ${query}` },
      { value: 'STORE002', label: `供应商B - ${query}` }
    ]
  }
}

// 查询风险规则
const handleQuery = async () => {
  loading.value = true
  hasQueried.value = true
  
  try {
    // 构建查询参数
    const params = buildQueryParams()
    
    // 调用API
    const response = await queryRiskRules(params)
    
    riskRules.value = response.d.results
    
    if (riskRules.value.length > 0) {
      ElMessage.warning(`检测到 ${riskRules.value.length} 条风险提示，请注意查看！`)
    } else {
      ElMessage.success('未检测到风险，可以继续操作')
    }
  } catch (error) {
    ElMessage.error('查询失败: ' + error.message)
  } finally {
    loading.value = false
  }
}

// 构建查询参数
const buildQueryParams = () => {
  const filters = []
  
  // 品类筛选
  if (filterForm.classList.length > 0) {
    const classFilters = filterForm.classList.map(item => {
      const classLevel = Array.isArray(item) ? item[item.length - 1] : item
      return `ClassA eq '${classLevel}' or ClassB eq '${classLevel}' or ClassC eq '${classLevel}' or ClassD eq '${classLevel}'`
    })
    filters.push(`(${classFilters.join(' or ')})`)
  }
  
  // 零件品牌
  if (filterForm.partsBrandList.length > 0) {
    const brandFilters = filterForm.partsBrandList.map(b => `PartsBrand eq '${b}'`)
    filters.push(`(${brandFilters.join(' or ')})`)
  }
  
  // 品质
  if (filterForm.qualityList.length > 0) {
    const qualityFilters = filterForm.qualityList.map(q => `Quality eq '${q}'`)
    filters.push(`(${qualityFilters.join(' or ')})`)
  }
  
  // 汽车品牌
  if (filterForm.carBrandList.length > 0) {
    const carBrandFilters = filterForm.carBrandList.map(c => `CarBrand eq '${c}'`)
    filters.push(`(${carBrandFilters.join(' or ')})`)
  }
  
  // 价格区间
  if (filterForm.priceRangeList.length > 0) {
    const priceFilters = filterForm.priceRangeList.map(p => `PriceRange eq '${p}'`)
    filters.push(`(${priceFilters.join(' or ')})`)
  }
  
  // 连队
  if (regionMode.value === 'region' && filterForm.regionList.length > 0) {
    const regionFilters = filterForm.regionList.map(r => `Region eq '${r}'`)
    filters.push(`(${regionFilters.join(' or ')})`)
  }
  
  // 省市区
  if (regionMode.value === 'zone' && filterForm.zoneList.length > 0) {
    const zoneFilters = filterForm.zoneList.map(zone => {
      const codes = Array.isArray(zone) ? zone : [zone]
      if (codes.length === 1) return `ProvinceCode eq '${codes[0]}'`
      if (codes.length === 2) return `CityCode eq '${codes[1]}'`
      if (codes.length === 3) return `CountyCode eq '${codes[2]}'`
      return ''
    }).filter(f => f)
    if (zoneFilters.length > 0) {
      filters.push(`(${zoneFilters.join(' or ')})`)
    }
  }
  
  // 维修厂
  if (filterForm.customerList.length > 0) {
    const customerFilters = filterForm.customerList.map(c => `CustomerId eq '${c}'`)
    filters.push(`(${customerFilters.join(' or ')})`)
  }
  
  // 供应商
  if (filterForm.storeList.length > 0) {
    const storeFilters = filterForm.storeList.map(s => `StoreId eq '${s}'`)
    filters.push(`(${storeFilters.join(' or ')})`)
  }
  
  // 车辆等级
  if (filterForm.carGradeList.length > 0) {
    const gradeFilters = filterForm.carGradeList.map(g => `CarGrade eq '${g}'`)
    filters.push(`(${gradeFilters.join(' or ')})`)
  }
  
  // 是否严选件
  if (filterForm.isStrict) {
    filters.push(`IsStrict eq '${filterForm.isStrict}'`)
  }
  
  // 只查询启用的规则
  filters.push(`Status eq 'A'`)
  
  return {
    $filter: filters.join(' and '),
    $expand: '_Files',
    $orderby: 'Priority desc',
    $format: 'json'
  }
}

// 重置
const handleReset = () => {
  Object.keys(filterForm).forEach(key => {
    if (Array.isArray(filterForm[key])) {
      filterForm[key] = []
    } else {
      filterForm[key] = ''
    }
  })
  riskRules.value = []
  hasQueried.value = false
}

// 获取优先级类型
const getPriorityType = (priority) => {
  if (priority >= 80) return 'danger'
  if (priority >= 50) return 'warning'
  return 'info'
}

// 获取文件图标
const getFileIcon = (fileType) => {
  switch (fileType) {
    case 'IMG': return Picture
    case 'VIDEO': return VideoCamera
    case 'PDF': return Document
    default: return Document
  }
}
</script>

<style scoped>
.risk-filter-panel {
  padding: 20px;
  background: #fff;
}

.risk-results {
  margin-top: 20px;
}

.risk-list {
  margin-top: 15px;
}

.risk-card {
  margin-bottom: 15px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.risk-title {
  font-weight: bold;
  font-size: 16px;
  display: flex;
  align-items: center;
  gap: 8px;
}

.risk-content {
  line-height: 1.8;
}

.risk-files {
  margin-top: 15px;
}

.file-list {
  display: flex;
  flex-direction: column;
  gap: 10px;
}
</style>
```

---

## 二、API 服务封装

### 2.1 API 配置 (api/risk.js)

```javascript
import request from '@/utils/request'

const BASE_PATH = '/sap/opu/odata/sap/ZUI_RISK_RULE_SRV'

/**
 * 查询风险规则
 * @param {Object} params - 查询参数
 * @returns {Promise}
 */
export function queryRiskRules(params) {
  return request({
    url: `${BASE_PATH}/RiskRuleSet`,
    method: 'get',
    params
  })
}

/**
 * 根据订单数据检查风险
 * @param {Object} orderData - 订单数据
 * @returns {Promise}
 */
export function checkOrderRisk(orderData) {
  // 构建过滤条件
  const filters = []
  
  if (orderData.classA) {
    filters.push(`ClassA eq '${orderData.classA}'`)
  }
  
  if (orderData.partsBrand) {
    filters.push(`PartsBrand eq '${orderData.partsBrand}'`)
  }
  
  if (orderData.quality) {
    filters.push(`Quality eq '${orderData.quality}'`)
  }
  
  if (orderData.carBrand) {
    filters.push(`CarBrand eq '${orderData.carBrand}'`)
  }
  
  if (orderData.price) {
    const priceRange = getPriceRangeCode(orderData.price)
    filters.push(`PriceRange eq '${priceRange}'`)
  }
  
  if (orderData.province) {
    filters.push(`ProvinceCode eq '${orderData.province}'`)
  }
  
  if (orderData.city) {
    filters.push(`CityCode eq '${orderData.city}'`)
  }
  
  if (orderData.county) {
    filters.push(`CountyCode eq '${orderData.county}'`)
  }
  
  if (orderData.customerId) {
    filters.push(`CustomerId eq '${orderData.customerId}'`)
  }
  
  if (orderData.storeId) {
    filters.push(`StoreId eq '${orderData.storeId}'`)
  }
  
  if (orderData.carGrade) {
    filters.push(`CarGrade eq '${orderData.carGrade}'`)
  }
  
  filters.push(`Status eq 'A'`)
  
  return request({
    url: `${BASE_PATH}/RiskRuleSet`,
    method: 'get',
    params: {
      $filter: filters.join(' and '),
      $expand: '_Files',
      $orderby: 'Priority desc',
      $format: 'json'
    }
  })
}

/**
 * 根据价格获取价格区间编码
 * @param {Number} price - 价格
 * @returns {String} - 价格区间编码
 */
function getPriceRangeCode(price) {
  if (price <= 100) return 'RANGE01'
  if (price <= 500) return 'RANGE02'
  if (price <= 1000) return 'RANGE03'
  if (price <= 2000) return 'RANGE04'
  if (price <= 3000) return 'RANGE05'
  return 'RANGE06'
}
```

---

## 三、实际业务集成示例

### 3.1 订单提交前风险检查

```vue
<template>
  <div class="order-submit-page">
    <el-form ref="orderFormRef" :model="orderForm" :rules="rules">
      <!-- 订单表单字段 -->
      
      <el-form-item>
        <el-button type="primary" @click="handleSubmit" :loading="submitting">
          提交订单
        </el-button>
      </el-form-item>
    </el-form>
    
    <!-- 风险提示对话框 -->
    <el-dialog
      v-model="riskDialogVisible"
      title="风险提示"
      width="700px"
      :close-on-click-modal="false"
    >
      <el-alert
        title="检测到以下风险,请仔细阅读后确认是否继续"
        type="warning"
        :closable="false"
        show-icon
      />
      
      <div class="risk-list-dialog">
        <el-card
          v-for="(risk, index) in detectedRisks"
          :key="index"
          class="risk-item"
        >
          <template #header>
            <div style="font-weight: bold; color: #e6a23c;">
              <el-icon><Warning /></el-icon>
              {{ risk.RiskTitle }}
            </div>
          </template>
          <div>
            <p>{{ risk.RiskContent }}</p>
            
            <!-- 附件 -->
            <div v-if="risk._Files && risk._Files.results.length > 0">
              <el-divider />
              <div>
                <el-link
                  v-for="file in risk._Files.results"
                  :key="file.FileId"
                  :href="file.FileUrl"
                  target="_blank"
                  style="margin-right: 10px;"
                >
                  {{ file.FileName }}
                </el-link>
              </div>
            </div>
          </div>
        </el-card>
      </div>
      
      <template #footer>
        <el-checkbox v-model="confirmRisk">
          我已了解上述风险，确认继续提交
        </el-checkbox>
        <div style="margin-top: 15px;">
          <el-button @click="riskDialogVisible = false">取消</el-button>
          <el-button
            type="primary"
            :disabled="!confirmRisk"
            @click="confirmSubmitOrder"
          >
            确认提交
          </el-button>
        </div>
      </template>
    </el-dialog>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
import { ElMessage } from 'element-plus'
import { Warning } from '@element-plus/icons-vue'
import { checkOrderRisk } from '@/api/risk'
import { submitOrder } from '@/api/order'

const orderFormRef = ref(null)
const orderForm = reactive({
  productId: '',
  classA: '',
  classB: '',
  partsBrand: '',
  quality: '',
  carBrand: '',
  price: 0,
  quantity: 1,
  province: '',
  city: '',
  county: '',
  customerId: '',
  storeId: '',
  carGrade: ''
})

const rules = {
  // 表单验证规则
}

const submitting = ref(false)
const riskDialogVisible = ref(false)
const detectedRisks = ref([])
const confirmRisk = ref(false)

// 提交订单
const handleSubmit = async () => {
  // 1. 表单验证
  const valid = await orderFormRef.value.validate()
  if (!valid) return
  
  // 2. 风险检查
  submitting.value = true
  try {
    const response = await checkOrderRisk(orderForm)
    
    if (response.d.results && response.d.results.length > 0) {
      // 检测到风险,显示提示
      detectedRisks.value = response.d.results
      riskDialogVisible.value = true
      confirmRisk.value = false
    } else {
      // 无风险,直接提交
      await doSubmitOrder()
    }
  } catch (error) {
    ElMessage.error('风险检查失败: ' + error.message)
  } finally {
    submitting.value = false
  }
}

// 确认提交订单(忽略风险)
const confirmSubmitOrder = async () => {
  riskDialogVisible.value = false
  await doSubmitOrder()
}

// 实际提交订单
const doSubmitOrder = async () => {
  submitting.value = true
  try {
    await submitOrder(orderForm)
    ElMessage.success('订单提交成功')
    // 跳转到订单列表等
  } catch (error) {
    ElMessage.error('订单提交失败: ' + error.message)
  } finally {
    submitting.value = false
  }
}
</script>

<style scoped>
.risk-list-dialog {
  margin-top: 15px;
  max-height: 400px;
  overflow-y: auto;
}

.risk-item {
  margin-bottom: 15px;
}
</style>
```

---

## 四、React + Ant Design 实现

### 4.1 风险检查 Hook

```typescript
// hooks/useRiskCheck.ts
import { useState } from 'react'
import { message } from 'antd'
import { checkOrderRisk } from '@/services/risk'

interface OrderData {
  classA?: string
  classB?: string
  partsBrand?: string
  quality?: string
  carBrand?: string
  price?: number
  province?: string
  city?: string
  county?: string
  customerId?: string
  storeId?: string
  carGrade?: string
}

interface RiskRule {
  RuleId: string
  RuleName: string
  RiskTitle: string
  RiskContent: string
  Priority: number
  _Files?: {
    results: Array<{
      FileId: string
      FileName: string
      FileType: string
      FileUrl: string
    }>
  }
}

export const useRiskCheck = () => {
  const [loading, setLoading] = useState(false)
  const [risks, setRisks] = useState<RiskRule[]>([])
  const [hasRisk, setHasRisk] = useState(false)
  
  const checkRisk = async (orderData: OrderData) => {
    setLoading(true)
    try {
      const response = await checkOrderRisk(orderData)
      const detectedRisks = response.d.results || []
      
      setRisks(detectedRisks)
      setHasRisk(detectedRisks.length > 0)
      
      if (detectedRisks.length > 0) {
        message.warning(`检测到 ${detectedRisks.length} 条风险提示`)
      }
      
      return {
        hasRisk: detectedRisks.length > 0,
        risks: detectedRisks
      }
    } catch (error) {
      message.error('风险检查失败')
      throw error
    } finally {
      setLoading(false)
    }
  }
  
  const clearRisks = () => {
    setRisks([])
    setHasRisk(false)
  }
  
  return {
    loading,
    risks,
    hasRisk,
    checkRisk,
    clearRisks
  }
}
```

### 4.2 风险提示组件

```tsx
// components/RiskAlert.tsx
import React from 'react'
import { Alert, Card, Typography, Space, Tag } from 'antd'
import { WarningOutlined, FileTextOutlined, PictureOutlined, VideoCameraOutlined } from '@ant-design/icons'

const { Text, Link } = Typography

interface RiskRule {
  RuleId: string
  RuleName: string
  RiskTitle: string
  RiskContent: string
  Priority: number
  _Files?: {
    results: Array<{
      FileId: string
      FileName: string
      FileType: string
      FileUrl: string
    }>
  }
}

interface RiskAlertProps {
  risks: RiskRule[]
  onClose?: () => void
}

const getFileIcon = (fileType: string) => {
  switch (fileType) {
    case 'IMG': return <PictureOutlined />
    case 'VIDEO': return <VideoCameraOutlined />
    case 'PDF': return <FileTextOutlined />
    default: return <FileTextOutlined />
  }
}

const getPriorityColor = (priority: number) => {
  if (priority >= 80) return 'red'
  if (priority >= 50) return 'orange'
  return 'blue'
}

export const RiskAlert: React.FC<RiskAlertProps> = ({ risks, onClose }) => {
  if (risks.length === 0) return null
  
  return (
    <Space direction="vertical" size="middle" style={{ width: '100%' }}>
      <Alert
        message={`检测到 ${risks.length} 条风险提示`}
        description="请仔细阅读以下风险提示内容"
        type="warning"
        showIcon
        icon={<WarningOutlined />}
        closable
        onClose={onClose}
      />
      
      {risks.map((risk) => (
        <Card
          key={risk.RuleId}
          title={
            <Space>
              <WarningOutlined style={{ color: '#faad14' }} />
              <Text strong>{risk.RiskTitle}</Text>
              <Tag color={getPriorityColor(risk.Priority)}>
                优先级: {risk.Priority}
              </Tag>
            </Space>
          }
          size="small"
        >
          <Text>{risk.RiskContent}</Text>
          
          {risk._Files && risk._Files.results.length > 0 && (
            <div style={{ marginTop: 16 }}>
              <Text type="secondary">相关附件:</Text>
              <Space direction="vertical" size="small" style={{ marginTop: 8 }}>
                {risk._Files.results.map((file) => (
                  <Link
                    key={file.FileId}
                    href={file.FileUrl}
                    target="_blank"
                  >
                    {getFileIcon(file.FileType)} {file.FileName}
                  </Link>
                ))}
              </Space>
            </div>
          )}
        </Card>
      ))}
    </Space>
  )
}
```

---

## 五、移动端集成 (Vant)

### 5.1 移动端风险检查组件

```vue
<template>
  <div class="mobile-risk-check">
    <van-popup
      v-model:show="visible"
      position="bottom"
      :style="{ height: '70%' }"
      closeable
      round
    >
      <div class="risk-popup-content">
        <div class="risk-header">
          <van-icon name="warning" color="#ff976a" size="24" />
          <span class="risk-title">风险提示</span>
        </div>
        
        <van-notice-bar
          left-icon="info-o"
          :text="`检测到 ${risks.length} 条风险提示，请仔细查看`"
        />
        
        <div class="risk-list">
          <van-cell-group v-for="risk in risks" :key="risk.RuleId">
            <van-cell>
              <template #title>
                <div class="risk-item-title">
                  <van-tag :type="getPriorityType(risk.Priority)">
                    优先级 {{ risk.Priority }}
                  </van-tag>
                  <span>{{ risk.RiskTitle }}</span>
                </div>
              </template>
              <template #label>
                <div class="risk-content">{{ risk.RiskContent }}</div>
                
                <!-- 附件 -->
                <div v-if="risk._Files && risk._Files.results.length > 0" class="risk-files">
                  <van-divider content-position="left">附件</van-divider>
                  <van-cell
                    v-for="file in risk._Files.results"
                    :key="file.FileId"
                    :title="file.FileName"
                    :icon="getFileIcon(file.FileType)"
                    is-link
                    @click="openFile(file.FileUrl)"
                  />
                </div>
              </template>
            </van-cell>
          </van-cell-group>
        </div>
        
        <div class="risk-footer">
          <van-checkbox v-model="confirmed">
            我已了解上述风险
          </van-checkbox>
          <div class="action-buttons">
            <van-button plain @click="handleCancel">取消</van-button>
            <van-button type="primary" :disabled="!confirmed" @click="handleConfirm">
              确认继续
            </van-button>
          </div>
        </div>
      </div>
    </van-popup>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'
import { showToast } from 'vant'

const props = defineProps({
  visible: Boolean,
  risks: {
    type: Array,
    default: () => []
  }
})

const emit = defineEmits(['update:visible', 'confirm', 'cancel'])

const confirmed = ref(false)

watch(() => props.visible, (newVal) => {
  if (newVal) {
    confirmed.value = false
  }
})

const getPriorityType = (priority) => {
  if (priority >= 80) return 'danger'
  if (priority >= 50) return 'warning'
  return 'primary'
}

const getFileIcon = (fileType) => {
  switch (fileType) {
    case 'IMG': return 'photo-o'
    case 'VIDEO': return 'video-o'
    case 'PDF': return 'description'
    default: return 'description'
  }
}

const openFile = (url) => {
  window.open(url, '_blank')
}

const handleCancel = () => {
  emit('update:visible', false)
  emit('cancel')
}

const handleConfirm = () => {
  if (!confirmed.value) {
    showToast('请先确认已了解风险')
    return
  }
  emit('update:visible', false)
  emit('confirm')
}
</script>

<style scoped>
.risk-popup-content {
  padding: 20px;
  height: 100%;
  display: flex;
  flex-direction: column;
}

.risk-header {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 16px;
}

.risk-title {
  font-size: 18px;
  font-weight: bold;
}

.risk-list {
  flex: 1;
  overflow-y: auto;
  margin: 16px 0;
}

.risk-item-title {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 8px;
}

.risk-content {
  margin-top: 8px;
  line-height: 1.6;
  color: #646566;
}

.risk-files {
  margin-top: 12px;
}

.risk-footer {
  border-top: 1px solid #eee;
  padding-top: 16px;
}

.action-buttons {
  display: flex;
  gap: 12px;
  margin-top: 16px;
}

.action-buttons .van-button {
  flex: 1;
}
</style>
```

---

## 六、总结

### 6.1 集成步骤

1. **安装依赖**
   ```bash
   npm install axios element-plus @element-plus/icons-vue
   ```

2. **配置 API**
   - 设置 baseURL
   - 配置请求拦截器
   - 配置响应拦截器

3. **封装服务**
   - 创建风险检查服务
   - 处理错误和异常

4. **开发组件**
   - 风险筛选面板
   - 风险提示展示
   - 风险确认对话框

5. **业务集成**
   - 订单提交前检查
   - 询价单创建检查
   - 实时风险监控

### 6.2 最佳实践

✅ **用户体验**
- 实时风险提示
- 清晰的视觉反馈
- 友好的错误提示

✅ **性能优化**
- 防抖处理
- 请求缓存
- 懒加载

✅ **安全性**
- 参数验证
- XSS 防护
- CSRF 防护

---

**文档版本**: v1.0  
**最后更新**: 2025-11-03

