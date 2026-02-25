# 安装说明

本文档提供项目的快速安装指南。

---

## 快速开始

### 1. 安装 Git

根据您的操作系统选择对应的安装方式：

**Windows:**
```bash
winget install Git.Git
```

**macOS:**
```bash
brew install git
```

**Linux (Ubuntu/Debian):**
```bash
sudo apt update && sudo apt install git -y
```

**Linux (CentOS/RHEL/Fedora):**
```bash
sudo dnf install git -y
```

### 2. 配置 Git

```bash
# 设置用户名（必填，用于提交记录标识）
git config --global user.name "您的用户名"

# 设置邮箱（必填，用于提交记录标识）
git config --global user.email "your_email@example.com"

# 设置默认分支名称（推荐）
git config --global init.defaultBranch main

# 解决中文文件名显示问题（推荐）
git config --global core.quotepath false
```

### 3. 克隆项目

```bash
# 使用 HTTPS 方式克隆
# 参数：<url> - 仓库地址
git clone https://github.com/your-org/git_pull.git

# 或使用 SSH 方式克隆（需要配置 SSH 密钥）
git clone git@github.com:your-org/git_pull.git

# 进入项目目录
cd git_pull
```

### 4. 验证安装

```bash
# 检查 Git 版本
git --version

# 查看仓库状态
git status

# 列出项目文件
ls -la
```

---

## 环境要求

| 组件 | 最低版本 | 推荐版本 |
|------|----------|----------|
| Git | 2.20+ | 2.40+ |
| 操作系统 | Windows 10 / macOS 10.14 / Ubuntu 18.04 | 最新 LTS 版本 |

---

## 项目文件概览

```
git_pull/
├── README.md                 # 项目简介
├── README2.md                # 详细安装说明
├── README3.md                # 快速安装指南（本文件）
├── Git操作手册.md            # Git 完整操作指南
│
├── 企业微信/微信集成
│   ├── send_qw.md                      # 企业微信消息发送
│   ├── wechat_api_usage_example.md     # 微信 API 示例
│   ├── get_openid_example.md           # OpenID 获取示例
│   ├── ZCL_CORPWEIXIN_APPROVAL_API.md  # 审批 API
│   └── ...
│
├── 风险筛选模块
│   ├── risk_filter_table_design.md     # 表设计文档
│   ├── risk_filter_odata_design.md     # OData 接口设计
│   ├── risk_filter_ui_integration.md   # UI 集成文档
│   └── ...
│
└── 其他文档
    ├── zgroup_odata_interface.md       # OData 接口文档
    └── ...
```

---

## 常见问题

### 克隆失败：权限被拒绝

配置 SSH 密钥：

```bash
# 生成密钥（-t 指定算法，-C 添加注释）
ssh-keygen -t ed25519 -C "your_email@example.com"

# 查看公钥并添加到 GitHub/GitLab
cat ~/.ssh/id_ed25519.pub
```

### 中文乱码

```bash
git config --global core.quotepath false
git config --global gui.encoding utf-8
```

### 更新项目

```bash
# 拉取最新代码（origin 为远程仓库，main 为分支名）
git pull origin main
```

---

## 下一步

- 查看 [Git操作手册.md](./Git操作手册.md) 了解详细的 Git 使用方法
- 查看 [README2.md](./README2.md) 获取更详细的安装说明

---

*最后更新：2026-02-25*
