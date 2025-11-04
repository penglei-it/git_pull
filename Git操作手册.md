# Cursor Git 操作手册

> 完整的 Git 版本控制操作指南，适用于 Cursor IDE
> 
> 作者：彭磊 | 更新日期：2025-11-04

---

## 📑 目录

1. [Git 基础概念](#git-基础概念)
2. [初始化配置](#初始化配置)
3. [日常操作流程](#日常操作流程)
4. [分支管理](#分支管理)
5. [远程仓库操作](#远程仓库操作)
6. [常见场景解决方案](#常见场景解决方案)
7. [高级操作](#高级操作)
8. [故障排除](#故障排除)
9. [最佳实践](#最佳实践)

---

## Git 基础概念

### 三个工作区域

```
工作区 (Working Directory)
    ↓ git add
暂存区 (Staging Area/Index)
    ↓ git commit
本地仓库 (Local Repository)
    ↓ git push
远程仓库 (Remote Repository)
```

### 文件状态

- **Untracked（未跟踪）**：新建的文件，Git 不知道它的存在
- **Modified（已修改）**：文件被修改但未暂存
- **Staged（已暂存）**：文件被添加到暂存区
- **Committed（已提交）**：文件已保存到本地仓库

---

## 初始化配置

### 1. 首次使用 Git 配置

```bash
# 设置用户名（必需）
git config --global user.name "您的姓名"

# 设置邮箱（必需）
git config --global user.email "your.email@example.com"

# 查看配置
git config --list

# 设置默认编辑器（可选）
git config --global core.editor "code --wait"

# 设置默认分支名为 main（推荐）
git config --global init.defaultBranch main
```

### 2. 初始化本地仓库

**方式一：从零开始**

```bash
# 在项目目录中初始化
git init

# 添加远程仓库
git remote add origin https://github.com/username/repository.git
```

**方式二：克隆现有仓库**

```bash
# 克隆远程仓库到本地
git clone https://github.com/username/repository.git

# 克隆到指定目录
git clone https://github.com/username/repository.git my-project
```

---

## 日常操作流程

### 标准工作流程

```bash
# 1. 查看当前状态
git status

# 2. 拉取最新代码（避免冲突）
git pull origin main

# 3. 进行代码修改...

# 4. 查看修改内容
git diff

# 5. 添加文件到暂存区
git add .                    # 添加所有修改
git add filename.txt         # 添加指定文件
git add *.js                 # 添加所有 .js 文件

# 6. 提交到本地仓库
git commit -m "feat: 添加用户登录功能"

# 7. 推送到远程仓库
git push origin main
```

### 查看操作

```bash
# 查看工作区状态
git status

# 查看修改内容
git diff                     # 查看未暂存的修改
git diff --staged            # 查看已暂存的修改
git diff HEAD                # 查看所有修改

# 查看提交历史
git log                      # 详细日志
git log --oneline            # 简洁日志
git log --graph --oneline    # 图形化日志
git log -n 5                 # 查看最近5条

# 查看某个文件的历史
git log --follow filename.txt

# 查看某次提交的详细信息
git show commit_id
```

---

## 分支管理

### 基本分支操作

```bash
# 查看所有分支
git branch                   # 本地分支
git branch -r                # 远程分支
git branch -a                # 所有分支

# 创建新分支
git branch feature-login

# 切换分支
git checkout feature-login

# 创建并切换分支（推荐）
git checkout -b feature-login

# 使用新命令（Git 2.23+）
git switch feature-login     # 切换分支
git switch -c feature-login  # 创建并切换

# 重命名分支
git branch -m old-name new-name

# 删除分支
git branch -d feature-login  # 安全删除（已合并）
git branch -D feature-login  # 强制删除
```

### 分支合并

```bash
# 合并分支到当前分支
git checkout main            # 先切换到目标分支
git merge feature-login      # 合并 feature-login 到 main

# 如果有冲突
# 1. 手动解决冲突文件
# 2. 标记为已解决
git add conflicted-file.txt
# 3. 完成合并
git commit
```

### 分支命名规范

```
main/master     - 主分支（生产环境）
develop         - 开发分支
feature/xxx     - 功能分支（如：feature/user-login）
bugfix/xxx      - 修复分支（如：bugfix/login-error）
hotfix/xxx      - 紧急修复（如：hotfix/security-patch）
release/xxx     - 发布分支（如：release/v1.0.0）
```

---

## 远程仓库操作

### 远程仓库管理

```bash
# 查看远程仓库
git remote -v

# 添加远程仓库
git remote add origin https://github.com/username/repo.git

# 修改远程仓库地址
git remote set-url origin https://github.com/username/new-repo.git

# 删除远程仓库
git remote remove origin

# 重命名远程仓库
git remote rename origin upstream
```

### 推送和拉取

```bash
# 推送到远程仓库
git push origin main                    # 推送到指定分支
git push -u origin main                 # 首次推送并设置上游
git push --all origin                   # 推送所有分支
git push --tags                         # 推送所有标签

# 拉取远程代码
git pull origin main                    # 拉取并合并
git pull --rebase origin main           # 拉取并变基

# 只获取不合并
git fetch origin                        # 获取所有远程更新
git fetch origin main                   # 获取指定分支
```

### 同步远程分支

```bash
# 删除远程已删除但本地还存在的分支
git remote prune origin

# 或者
git fetch --prune

# 删除远程分支
git push origin --delete feature-login
```

---

## 常见场景解决方案

### 场景 1：撤销修改

```bash
# 撤销工作区的修改（未 add）
git checkout -- filename.txt         # 单个文件
git checkout .                       # 所有文件

# 撤销暂存区的文件（已 add 未 commit）
git reset HEAD filename.txt          # 单个文件
git reset HEAD                       # 所有文件

# 撤销最近一次提交（已 commit 未 push）
git reset --soft HEAD~1              # 保留修改，撤销提交
git reset --mixed HEAD~1             # 默认，撤销提交和暂存
git reset --hard HEAD~1              # ⚠️ 完全撤销，丢失修改

# 修改最后一次提交信息
git commit --amend -m "新的提交信息"

# 修改最后一次提交内容（补充文件）
git add forgotten-file.txt
git commit --amend --no-edit
```

### 场景 2：处理推送被拒绝

**错误信息：**
```
! [rejected] main -> main (fetch first)
error: failed to push some refs
```

**解决方案：**

```bash
# 方案1：拉取并合并（推荐）
git pull origin main
# 解决冲突（如果有）
git push origin main

# 方案2：拉取并变基（保持历史清晰）
git pull --rebase origin main
# 解决冲突（如果有）
git push origin main

# 方案3：强制推送（⚠️ 谨慎使用）
git push --force origin main
```

### 场景 3：合并不相关的历史

**错误信息：**
```
fatal: refusing to merge unrelated histories
```

**解决方案：**

```bash
# 允许合并不相关的历史
git pull origin main --allow-unrelated-histories

# 解决可能的冲突后推送
git push origin main
```

### 场景 4：解决合并冲突

```bash
# 1. 尝试合并时发生冲突
git merge feature-branch
# Auto-merging file.txt
# CONFLICT (content): Merge conflict in file.txt

# 2. 查看冲突文件
git status

# 3. 手动编辑冲突文件，冲突标记如下：
<<<<<<< HEAD
当前分支的内容
=======
要合并分支的内容
>>>>>>> feature-branch

# 4. 保留需要的内容，删除标记

# 5. 标记为已解决
git add file.txt

# 6. 完成合并
git commit

# 如果想放弃合并
git merge --abort
```

### 场景 5：暂存当前工作

```bash
# 临时保存当前工作（切换分支前）
git stash
git stash save "工作描述"

# 查看暂存列表
git stash list

# 恢复暂存的工作
git stash pop                # 恢复并删除最近的暂存
git stash apply              # 恢复但保留暂存
git stash apply stash@{0}    # 恢复指定暂存

# 删除暂存
git stash drop stash@{0}     # 删除指定暂存
git stash clear              # 删除所有暂存
```

### 场景 6：回退到历史版本

```bash
# 查看提交历史
git log --oneline

# 回退到指定版本（保留修改）
git reset --soft commit_id

# 回退到指定版本（不保留修改）⚠️
git reset --hard commit_id

# 创建一个反向提交（推荐用于已推送的代码）
git revert commit_id
```

### 场景 7：清理未跟踪的文件

```bash
# 查看会删除哪些文件（预览）
git clean -n

# 删除未跟踪的文件
git clean -f

# 删除未跟踪的文件和目录
git clean -fd

# 包括被 .gitignore 忽略的文件
git clean -fdx
```

---

## 高级操作

### 标签管理

```bash
# 创建轻量标签
git tag v1.0.0

# 创建带注释的标签（推荐）
git tag -a v1.0.0 -m "版本 1.0.0 发布"

# 给历史提交打标签
git tag -a v0.9.0 commit_id -m "版本 0.9.0"

# 查看所有标签
git tag
git tag -l "v1.*"

# 查看标签信息
git show v1.0.0

# 推送标签到远程
git push origin v1.0.0       # 推送单个标签
git push origin --tags       # 推送所有标签

# 删除标签
git tag -d v1.0.0            # 删除本地标签
git push origin :refs/tags/v1.0.0  # 删除远程标签
```

### 变基（Rebase）

```bash
# 变基到主分支
git checkout feature-branch
git rebase main

# 交互式变基（整理提交历史）
git rebase -i HEAD~3         # 整理最近3次提交

# 变基过程中解决冲突
# 1. 解决冲突文件
# 2. 添加已解决的文件
git add conflicted-file.txt
# 3. 继续变基
git rebase --continue

# 放弃变基
git rebase --abort
```

### Cherry-Pick（挑选提交）

```bash
# 将其他分支的某个提交应用到当前分支
git cherry-pick commit_id

# 挑选多个提交
git cherry-pick commit_id1 commit_id2

# 挑选后不自动提交
git cherry-pick -n commit_id
```

### 子模块管理

```bash
# 添加子模块
git submodule add https://github.com/user/repo.git path/to/submodule

# 克隆包含子模块的仓库
git clone --recursive https://github.com/user/repo.git

# 初始化子模块
git submodule init
git submodule update

# 更新子模块
git submodule update --remote

# 删除子模块
git submodule deinit path/to/submodule
git rm path/to/submodule
```

---

## 故障排除

### 常见错误及解决

#### 1. 权限被拒绝

```bash
# 错误：Permission denied (publickey)
# 解决：配置 SSH 密钥或使用 HTTPS

# 生成 SSH 密钥
ssh-keygen -t ed25519 -C "your.email@example.com"

# 添加到 ssh-agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# 将公钥添加到 GitHub/GitLab
cat ~/.ssh/id_ed25519.pub
```

#### 2. 分支落后

```bash
# 错误：Your branch is behind 'origin/main'
# 解决：
git pull origin main
```

#### 3. 文件过大

```bash
# 错误：file exceeds GitHub's file size limit
# 解决：使用 Git LFS

# 安装 Git LFS
git lfs install

# 跟踪大文件
git lfs track "*.psd"
git add .gitattributes

# 提交和推送
git add file.psd
git commit -m "添加大文件"
git push origin main
```

#### 4. 提交历史混乱

```bash
# 使用 rebase 整理历史
git rebase -i HEAD~5

# 在编辑器中：
# pick = 保留提交
# reword = 修改提交信息
# squash = 合并到前一个提交
# drop = 删除提交
```

### 恢复操作

```bash
# 查看所有操作历史（包括已删除的提交）
git reflog

# 恢复到某个历史状态
git reset --hard HEAD@{2}

# 恢复已删除的分支
git checkout -b recovered-branch commit_id
```

---

## 最佳实践

### 提交信息规范

使用约定式提交（Conventional Commits）：

```
<类型>(<范围>): <描述>

[可选的正文]

[可选的脚注]
```

**类型（Type）：**

- `feat`: 新功能
- `fix`: 修复 Bug
- `docs`: 文档更新
- `style`: 代码格式（不影响功能）
- `refactor`: 重构
- `perf`: 性能优化
- `test`: 测试相关
- `chore`: 构建过程或辅助工具的变动

**示例：**

```bash
git commit -m "feat(auth): 添加用户登录功能"
git commit -m "fix(api): 修复数据获取错误"
git commit -m "docs: 更新 README 安装说明"
git commit -m "refactor: 重构用户服务模块"
```

### .gitignore 配置

创建 `.gitignore` 文件，忽略不需要版本控制的文件：

```gitignore
# 依赖目录
node_modules/
vendor/
.venv/

# 构建输出
dist/
build/
*.exe
*.dll
*.so

# 环境配置
.env
.env.local
config/local.json

# IDE 配置
.vscode/
.idea/
*.sublime-*

# 系统文件
.DS_Store
Thumbs.db
desktop.ini

# 日志文件
*.log
logs/

# 临时文件
*.tmp
*.temp
*.swp
*.swo
*~

# 数据库
*.sqlite
*.db
```

### 工作流建议

#### 1. **Feature Branch Workflow（功能分支工作流）**

```bash
# 1. 从主分支创建功能分支
git checkout main
git pull origin main
git checkout -b feature/user-profile

# 2. 在功能分支上开发
# ... 进行开发 ...
git add .
git commit -m "feat: 添加用户资料页面"

# 3. 推送到远程
git push origin feature/user-profile

# 4. 创建 Pull Request / Merge Request

# 5. 代码审查通过后合并到主分支
git checkout main
git pull origin main
git merge feature/user-profile
git push origin main

# 6. 删除功能分支
git branch -d feature/user-profile
git push origin --delete feature/user-profile
```

#### 2. **Git Flow 工作流**

```bash
# 主要分支
main        # 生产环境
develop     # 开发环境

# 功能开发
git checkout -b feature/xxx develop
# 开发完成后合并回 develop
git checkout develop
git merge feature/xxx

# 发布版本
git checkout -b release/v1.0.0 develop
# 测试和修复后合并到 main 和 develop
git checkout main
git merge release/v1.0.0
git tag v1.0.0
git checkout develop
git merge release/v1.0.0

# 紧急修复
git checkout -b hotfix/xxx main
# 修复后合并到 main 和 develop
git checkout main
git merge hotfix/xxx
git checkout develop
git merge hotfix/xxx
```

### 安全建议

```bash
# ⚠️ 永远不要提交敏感信息
# - 密码、API 密钥、令牌
# - 数据库连接字符串
# - 私钥文件

# 如果不小心提交了敏感信息
# 1. 立即修改密码/密钥
# 2. 从历史中移除（复杂操作，参考 git-filter-repo）

# 使用环境变量存储敏感信息
# 在 .gitignore 中添加 .env 文件
```

### 团队协作建议

1. **定期同步**
   ```bash
   # 每天开始工作前
   git pull origin main
   ```

2. **小而频繁的提交**
   - 每个提交只做一件事
   - 提交信息清晰明了
   - 经常推送到远程

3. **代码审查**
   - 使用 Pull Request / Merge Request
   - 至少一人审查代码
   - 通过 CI/CD 自动测试

4. **保护主分支**
   - 在 GitHub/GitLab 设置分支保护
   - 要求 PR 审查通过
   - 要求 CI 测试通过

---

## 快速参考

### 常用命令速查表

| 命令 | 说明 |
|------|------|
| `git status` | 查看状态 |
| `git add .` | 添加所有文件 |
| `git commit -m "msg"` | 提交 |
| `git push` | 推送 |
| `git pull` | 拉取 |
| `git log` | 查看历史 |
| `git diff` | 查看差异 |
| `git branch` | 查看分支 |
| `git checkout -b xxx` | 创建并切换分支 |
| `git merge xxx` | 合并分支 |
| `git stash` | 暂存工作 |

### 在 Cursor 中使用 Git

#### 方式 1：使用内置 Git 功能

Cursor 内置了 VS Code 的 Git 功能：

1. **源代码管理面板**：侧边栏的 Git 图标
2. **暂存文件**：点击文件旁的 "+" 号
3. **提交**：在消息框输入提交信息，按 Ctrl+Enter
4. **推送/拉取**：点击状态栏的同步按钮
5. **分支切换**：点击左下角分支名

#### 方式 2：使用终端（推荐）

在 Cursor 中打开终端：
- Windows: `` Ctrl+` ``
- Mac: `` Cmd+` ``

然后使用 Git 命令行操作。

---

## 学习资源

### 官方文档
- [Git 官方文档](https://git-scm.com/doc)
- [Pro Git 书籍](https://git-scm.com/book/zh/v2)（中文版）

### 在线练习
- [Learn Git Branching](https://learngitbranching.js.org/?locale=zh_CN)（交互式学习）
- [GitHub Skills](https://skills.github.com/)

### 可视化工具
- GitKraken
- SourceTree
- GitHub Desktop

---

## 总结

Git 是一个强大的版本控制工具，掌握它需要时间和实践。记住这些要点：

✅ **经常提交**：小步快跑，频繁提交
✅ **写好提交信息**：清晰描述做了什么
✅ **先拉取后推送**：避免冲突
✅ **使用分支**：不要在主分支直接开发
✅ **定期备份**：推送到远程仓库
✅ **保护敏感信息**：使用 .gitignore

---

## 更新日志

- **2025-11-04**: 创建初始版本，包含基础到高级的所有常用操作

---

如有问题或建议，请联系：lei.peng1@casstime.com

