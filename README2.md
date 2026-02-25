# 项目安装说明

本文档提供详细的安装与配置指南，帮助您快速部署和使用本项目。

---

## 目录

1. [环境要求](#环境要求)
2. [安装步骤](#安装步骤)
3. [配置说明](#配置说明)
4. [验证安装](#验证安装)
5. [常见问题](#常见问题)

---

## 环境要求

### 系统要求

| 项目 | 最低要求 | 推荐配置 |
|------|----------|----------|
| 操作系统 | Windows 10 / macOS 10.14 / Ubuntu 18.04 | Windows 11 / macOS 12+ / Ubuntu 22.04 |
| Git | 2.20+ | 2.40+ |
| 文本编辑器 | 任意 | VS Code / Cursor |

### 软件依赖

- **Git**: 用于版本控制和克隆仓库
- **Markdown 阅读器**: 用于查看和编辑文档（推荐使用 VS Code 或 Cursor）

---

## 安装步骤

### 1. 安装 Git

#### Windows

```bash
# 使用 winget 安装
winget install Git.Git

# 或从官网下载安装程序
# https://git-scm.com/download/win
```

#### macOS

```bash
# 使用 Homebrew 安装
brew install git

# 或使用 Xcode Command Line Tools
xcode-select --install
```

#### Linux (Ubuntu/Debian)

```bash
sudo apt update
sudo apt install git
```

#### Linux (CentOS/RHEL)

```bash
sudo yum install git
# 或
sudo dnf install git
```

### 2. 配置 Git

```bash
# 配置用户名和邮箱
# 参数说明：
# --global: 全局配置，对所有仓库生效
# user.name: 用户名，用于提交记录
# user.email: 邮箱，用于提交记录
git config --global user.name "您的用户名"
git config --global user.email "您的邮箱@example.com"

# 配置默认编辑器（可选）
git config --global core.editor "code --wait"

# 配置默认分支名称
git config --global init.defaultBranch main
```

### 3. 克隆仓库

```bash
# 克隆仓库到本地
# 参数说明：
# <repository-url>: 仓库地址
# [directory]: 可选，指定本地目录名称
git clone <repository-url> [directory]

# 示例
git clone https://github.com/your-org/git_pull.git
```

### 4. 进入项目目录

```bash
cd git_pull
```

---

## 配置说明

### 项目结构

```
git_pull/
├── README.md                    # 项目简介
├── README2.md                   # 安装说明（本文件）
├── Git操作手册.md               # Git 操作完整指南
├── aa.md                        # 附加文档
│
├── 微信/企业微信集成文档/
│   ├── send_qw.md              # 企业微信消息发送
│   ├── wechat_api_usage_example.md    # 微信 API 使用示例
│   ├── send_news_to_qw_group.md       # 发送图文到企业微信群
│   ├── send_mixed_message_qw_example.md # 混合消息发送示例
│   ├── send_qw_news_direct.md         # 直接发送图文消息
│   ├── send_qw_news_integration.md    # 图文消息集成
│   ├── send_news_on_click.md          # 点击发送图文
│   ├── get_openid_example.md          # 获取 OpenID 示例
│   ├── create_wechat_config_table.md  # 微信配置表创建
│   ├── zcl_wechat_official_api.md     # 微信公众号 API
│   ├── ZCL_CORPWEIXIN_APPROVAL_API.md # 企业微信审批 API
│   └── ZCL_WECHAT_MSG_CONVERTER.md    # 微信消息转换器
│
├── 风险筛选模块文档/
│   ├── risk_filter_table_design.md    # 表设计
│   ├── risk_filter_odata_design.md    # OData 设计
│   ├── risk_filter_rap_simple.md      # RAP 简化版
│   ├── risk_filter_ui_integration.md  # UI 集成
│   └── risk_filter_api_test.md        # API 测试
│
├── 其他文档/
│   ├── send.md                  # 发送功能说明
│   ├── zcl_cassint_notice.md    # 通知类说明
│   └── zgroup_odata_interface.md # OData 接口文档
│
└── .git/                        # Git 版本控制目录
```

### 文档分类说明

| 分类 | 说明 | 主要文件 |
|------|------|----------|
| 微信集成 | 企业微信/微信公众号 API 集成 | `send_qw.md`, `wechat_api_usage_example.md` |
| 风险筛选 | 风险筛选模块设计与实现 | `risk_filter_*.md` |
| Git 指南 | Git 版本控制操作手册 | `Git操作手册.md` |
| SAP/ABAP | SAP 系统相关代码文档 | `ZCL_*.md`, `zcl_*.md` |

---

## 验证安装

### 1. 检查 Git 安装

```bash
# 检查 Git 版本
git --version

# 预期输出示例：git version 2.40.0
```

### 2. 检查仓库状态

```bash
# 进入项目目录后执行
git status

# 预期输出：显示当前分支和工作区状态
```

### 3. 查看项目文件

```bash
# 列出所有文件
ls -la

# Windows 用户使用
dir
```

### 4. 打开文档

使用您的 Markdown 编辑器打开任意 `.md` 文件进行查看：

```bash
# 使用 VS Code 打开
code .

# 使用 Cursor 打开
cursor .
```

---

## 常见问题

### Q1: 克隆仓库时提示权限不足

**解决方案：**

1. 确认您有仓库访问权限
2. 使用 SSH 方式克隆：
   ```bash
   git clone git@github.com:your-org/git_pull.git
   ```
3. 配置 SSH 密钥：
   ```bash
   # 生成 SSH 密钥
   # 参数说明：
   # -t ed25519: 使用 Ed25519 算法
   # -C: 添加注释（通常为邮箱）
   ssh-keygen -t ed25519 -C "your_email@example.com"
   
   # 添加到 SSH Agent
   eval "$(ssh-agent -s)"
   ssh-add ~/.ssh/id_ed25519
   ```

### Q2: Git 命令提示 "not a git repository"

**解决方案：**

确保您在正确的目录下执行命令：

```bash
# 切换到项目目录
cd /path/to/git_pull

# 验证是否为 Git 仓库
ls -la .git
```

### Q3: 中文文件名显示乱码

**解决方案：**

```bash
# 配置 Git 正确显示中文
git config --global core.quotepath false
git config --global gui.encoding utf-8
git config --global i18n.commit.encoding utf-8
git config --global i18n.logoutputencoding utf-8
```

### Q4: Markdown 文件显示格式不正确

**解决方案：**

1. 使用支持 Markdown 的编辑器（VS Code、Cursor、Typora 等）
2. 安装 Markdown 预览插件
3. 确保文件编码为 UTF-8

### Q5: 如何更新到最新版本

**解决方案：**

```bash
# 拉取远程最新代码
# 参数说明：
# origin: 远程仓库名称
# main: 分支名称
git pull origin main
```

---

## 获取帮助

如果您在安装过程中遇到问题，可以：

1. 查阅 [Git操作手册.md](./Git操作手册.md) 获取详细的 Git 使用指南
2. 查看项目 Issues 页面
3. 联系项目维护人员

---

## 相关链接

- [Git 官方文档](https://git-scm.com/doc)
- [GitHub 帮助文档](https://docs.github.com)
- [VS Code 下载](https://code.visualstudio.com)
- [Cursor 下载](https://cursor.sh)

---

*文档版本：1.0.0 | 最后更新：2026-02-25*
