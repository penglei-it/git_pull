# SAP ABAP 企业微信与微信公众号集成项目

## 项目简介

本项目是一个基于 SAP ABAP 的企业微信与微信公众号集成解决方案，提供以下核心功能：

- **企业微信消息推送** - 支持文本、图文、卡片等多种消息类型
- **微信公众号 API 集成** - 包括 Access Token 管理、模板消息发送等
- **风险规则配置与过滤** - 基于多维度条件的风险提示系统
- **OData 接口服务** - 标准化的数据查询接口

## 安装说明

### 前置条件

在开始安装之前，请确保满足以下条件：

1. **SAP 系统要求**
   - SAP NetWeaver 7.50 或更高版本
   - ABAP Platform 1909 或更高版本（推荐）
   - 启用 OData 服务支持

2. **权限要求**
   - 拥有 ABAP 开发权限（S_DEVELOP 授权对象）
   - 具有传输请求创建和发布权限
   - 具有 SM59 RFC 目标配置权限

3. **外部服务要求**
   - 企业微信管理员账号及 CorpID
   - 微信公众号 AppID 和 AppSecret
   - 网络可访问企业微信/微信 API 服务器

### 安装步骤

#### 步骤一：导入 ABAP 代码

1. 使用 SE80/ADT 创建新的开发包（建议命名：`ZWECHAT_INTEGRATION`）

2. 导入以下核心类文件：
   - `ZCL_WECHAT_OFFICIAL_API` - 微信公众号 API 服务类
   - `ZCL_CORPWEIXIN_APPROVAL_API` - 企业微信审批 API 类
   - `ZCL_WECHAT_MSG_CONVERTER` - 消息格式转换类

3. 创建相关数据库表：
   ```abap
   " 参考 create_wechat_config_table.md 文档创建配置表
   " 参考 risk_filter_table_design.md 创建风险规则表
   ```

#### 步骤二：配置 RFC 目标

1. 进入事务代码 **SM59**

2. 创建 HTTP 类型的 RFC 目标：
   
   | 参数 | 企业微信 | 微信公众号 |
   |------|----------|------------|
   | RFC 目标名称 | `ZWECHAT_CORP` | `ZWECHAT_OFFICIAL` |
   | 目标主机 | `qyapi.weixin.qq.com` | `api.weixin.qq.com` |
   | 服务号 | `443` | `443` |
   | 路径前缀 | `/cgi-bin/` | `/cgi-bin/` |

3. 在「登录和安全」标签页中：
   - SSL 证书：选择「SSL 客户端（标准）」
   - 确保 SSL 证书已正确配置（STRUST 事务）

#### 步骤三：配置微信凭证

1. 创建配置表条目或使用安全存储：
   ```abap
   " 企业微信配置示例
   " CorpID: 企业ID
   " AgentID: 应用ID  
   " Secret: 应用密钥
   
   " 微信公众号配置示例
   " AppID: 公众号应用ID
   " AppSecret: 公众号应用密钥
   ```

2. **安全建议**：敏感信息（如 Secret）建议存储在安全存储（Secure Store）中

#### 步骤四：激活 OData 服务

1. 进入事务代码 **/IWFND/MAINT_SERVICE**

2. 添加并激活以下服务：
   - 风险规则配置服务
   - 消息推送服务

3. 为相关用户分配服务访问权限

#### 步骤五：测试安装

1. **测试 Access Token 获取**：
   ```abap
   DATA(lo_api) = NEW zcl_wechat_official_api(
     iv_appid      = 'YOUR_APPID'
     iv_appsecret  = 'YOUR_APPSECRET'
     iv_destination = 'ZWECHAT_OFFICIAL'
   ).
   DATA(lv_token) = lo_api->get_wechat_token( ).
   ```

2. **测试消息发送**：
   ```abap
   " 参考 send_qw.md 和 wechat_api_usage_example.md 文档
   ```

## 目录结构说明

```
├── README.md                      # 项目简介
├── README1.md                     # 安装说明（本文件）
├── Git操作手册.md                  # Git 使用指南
│
├── 核心 API 文档
│   ├── ZCL_CORPWEIXIN_APPROVAL_API.md  # 企业微信审批 API
│   ├── ZCL_WECHAT_MSG_CONVERTER.md     # 消息转换器
│   └── zcl_wechat_official_api.md      # 微信公众号 API
│
├── 消息发送示例
│   ├── send.md                    # 基础发送示例
│   ├── send_qw.md                 # 企业微信发送详解
│   ├── send_qw_news_direct.md     # 图文消息直接发送
│   ├── send_qw_news_integration.md # 图文消息集成
│   ├── send_news_on_click.md      # 点击事件处理
│   ├── send_news_to_qw_group.md   # 群消息发送
│   ├── send_mixed_message_qw_example.md # 混合消息示例
│   └── wechat_api_usage_example.md # API 使用示例
│
├── 风险过滤模块
│   ├── risk_filter_api_test.md    # API 测试文档
│   ├── risk_filter_odata_design.md # OData 接口设计
│   ├── risk_filter_rap_simple.md  # RAP 简化实现
│   ├── risk_filter_table_design.md # 数据表设计
│   └── risk_filter_ui_integration.md # UI 集成指南
│
├── 配置与设置
│   ├── create_wechat_config_table.md # 配置表创建
│   ├── get_openid_example.md      # OpenID 获取示例
│   └── zgroup_odata_interface.md  # 群组 OData 接口
│
└── 其他文档
    └── zcl_cassint_notice.md      # 通知相关
```

## 常见问题

### Q1: Access Token 获取失败

**可能原因**：
- RFC 目标配置错误
- SSL 证书未正确配置
- AppID/AppSecret 不正确

**解决方案**：
1. 检查 SM59 中的 RFC 目标连接测试
2. 使用 STRUST 事务检查 SSL 证书
3. 确认微信管理后台的凭证信息

### Q2: 消息发送返回错误

**可能原因**：
- 接收者 OpenID 无效
- 消息格式不正确
- Access Token 已过期

**解决方案**：
1. 验证 OpenID 是否正确关注了公众号
2. 参考消息格式文档检查 JSON 结构
3. 实现 Token 自动刷新机制

### Q3: OData 服务无法访问

**可能原因**：
- 服务未激活
- 用户缺少访问权限
- ICF 节点未激活

**解决方案**：
1. 在 /IWFND/MAINT_SERVICE 中检查服务状态
2. 为用户分配相应授权
3. 使用 SICF 事务激活相关节点

## 技术支持

如遇到问题，请参考以下资源：

- 企业微信开发文档：https://developer.work.weixin.qq.com/
- 微信公众号开发文档：https://developers.weixin.qq.com/
- SAP OData 开发指南：SAP Help Portal

## 版本历史

| 版本 | 日期 | 说明 |
|------|------|------|
| 1.0.0 | 2026-02-25 | 初始版本 |

## 许可证

本项目仅供内部使用，未经授权不得对外分发。
