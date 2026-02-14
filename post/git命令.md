---
author: Ryou
title: git命令
date: 2026-02-13T17:21:16+08:00
lastmod: 
description: 
draft: true
mermaid: true
image: https://w.wallhaven.cc/full/og/wallhaven-ogy6zm.jpg
位置: 上海市 - 上海市
坐标: 
  - 121.4768
  - 31.2243
温度: 18.6℃ 
风速: 6.6 m/s
天气: 多云
categories:
  - 
tags:
  - 
---

---

### 1. Git 核心原理深度解析

```mermaid
gitGraph
   commit id: "Initial"
   branch develop
   commit id: "Add feature"
   checkout main
   commit id: "Hotfix"
   merge develop
   commit id: "Release v1.0"
```

---

### 2. 详细使用场景与 GitGraph

#### 场景 1：完整功能开发流程

```mermaid
gitGraph
   commit id: "Initial commit"
   branch feature-login
   commit id: "Add login page"
   commit id: "Add validation"
   checkout main
   commit id: "Update config"
   checkout feature-login
   commit id: "Fix validation bug"
   checkout main
   merge feature-login id: "Merge login feature"
   commit id: "Release v1.0"
```

**详细命令流程**：

```bash
# 1. 确保主分支最新
git checkout main
git pull origin main

# 2. 创建功能分支
git checkout -b feature-login

# 3. 开发过程中的提交
git add login.go
git commit -m "feat: 添加登录页面"

git add validation.go
git commit -m "feat: 添加表单验证"

# 4. 定期同步主分支更新
git fetch origin
git merge origin/main

# 5. 开发完成后，合并到主分支
git checkout main
git merge feature-login

# 6. 推送并清理分支
git push origin main
git branch -d feature-login
```

---

#### 场景 2：Bug 修复流程（Hotfix）

```mermaid
gitGraph
   commit id: "v1.0 Release"
   commit id: "v1.1 Release"
   commit id: "v1.2 Release"
   branch hotfix-crash
   commit id: "Fix crash bug"
   commit id: "Add test case"
   checkout main
   merge hotfix-crash id: "Hotfix merge"
   commit id: "v1.2.1 Patch"
   branch release-2.0
   commit id: "Prepare v2.0"
```

**Hotfix 场景详解**：

```mermaid
gitGraph
   commit id: "Normal: A"
   commit id: "Normal: B"
   commit id: "Normal: C"
   branch hotfix
   commit id: "Hotfix: D"
   commit id: "Hotfix: E"
   checkout main
   merge hotfix id: "Merge hotfix"
   commit id: "Normal: F"
   commit id: "Normal: G"
```

```bash
# 发现线上 Bug，立即修复
git checkout main
git pull
git checkout -b hotfix-crash-bug

# 修复 Bug
git add fix.go
git commit -m "fix: 修复登录崩溃问题"

# 立即合并到主分支
git checkout main
git merge hotfix-crash-bug

# 推送并打标签
git push origin main
git tag -a v1.2.1 -m "紧急修复"

# 同时合并回开发分支（如果需要）
git checkout develop
git merge hotfix-crash-bug

# 清理
git branch -d hotfix-crash-bug
```

---

#### 场景 3：多人协作开发

```mermaid
gitGraph
   commit id: "Initial"
   branch feature-a
   branch feature-b
   checkout feature-a
   commit id: "Alice: Add A1"
   commit id: "Alice: Add A2"
   checkout feature-b
   commit id: "Bob: Add B1"
   commit id: "Bob: Add B2"
   checkout main
   merge feature-a id: "Merge A"
   merge feature-b id: "Merge B"
```

**协作冲突处理**：

```mermaid
gitGraph
   commit id: "Start"
   branch alice-feature
   commit id: "Alice: change 1"
   commit id: "Alice: change 2"
   checkout main
   commit id: "Bob: change 1"
   commit id: "Bob: change 2"
   checkout alice-feature
   commit id: "Alice: change 3"
   checkout main
   merge alice-feature id: "Conflict resolved"
```

```bash
# Alice 的操作
git checkout -b alice-feature
git add file.txt
git commit -m "Alice: 添加功能A"

# Bob 同时在 main 上开发
git checkout main
git add file.txt
git commit -m "Bob: 添加功能B"

# Alice 准备合并前，先同步 Bob 的更改
git fetch origin
git merge origin/main
# 如果有冲突，解决后
git add file.txt
git commit -m "解决合并冲突"

# 合并 Alice 的功能
git merge alice-feature
```

---

#### 场景 4：代码回滚场景

```mermaid
gitGraph
   commit id: "A"
   commit id: "B"
   commit id: "C"
   commit id: "D"
   commit id: "E"
```

**场景 4.1：本地未推送，想撤销**

```mermaid
gitGraph
   commit id: "A"
   commit id: "B"
   commit id: "C (撤销)"
   checkout HEAD~2
   commit id: "A"
   commit id: "B"
   commit id: "C'"
```

```bash
# 撤销最后一次提交（本地）
git reset --hard HEAD~1
git reset --hard abc123  # 回退到指定提交
```

**场景 4.2：已推送，想撤销（保留历史）**

```mermaid
gitGraph
   commit id: "A"
   commit id: "B"
   commit id: "C"
   commit id: "D"
   revert id: "Revert C"
```

```bash
# 撤销已推送的提交（创建新提交来撤销）
git revert HEAD
git revert abc123

# 推送到远程
git push origin main
```

**场景 4.3：撤销指定文件**

```mermaid
gitGraph
   commit id: "A: add file.txt"
   commit id: "B: modify file.txt"
   commit id: "C: other changes"
   checkout B -- file.txt
   commit id: "D: restore file.txt"
```

```bash
# 恢复单个文件到某个版本
git checkout abc123 -- filename.txt
git checkout HEAD~2 -- filename.txt
```

---

#### 场景 5：Git Flow 工作流

```mermaid
gitGraph
   commit id: "Initial"
   branch develop
   commit id: "Dev: Feature 1"
   commit id: "Dev: Feature 2"
   branch feature-x
   commit id: "Feature X: 1"
   commit id: "Feature X: 2"
   checkout develop
   merge feature-x id: "Merge X"
   commit id: "Dev: Feature 3"
   branch release-1.0
   commit id: "Release: Fix 1"
   commit id: "Release: Fix 2"
   checkout main
   merge release-1.0 id: "Release v1.0"
   commit id: "Dev: Continue"
   branch hotfix
   commit id: "Hotfix: Fix bug"
   checkout main
   merge hotfix id: "Hotfix merge"
   merge hotfix id: "Merge to develop"
```

**Git Flow 详细命令**：

```bash
# 1. 初始化 Git Flow
git flow init

# 2. 开始新功能
git flow feature start login
# 开发...
git flow feature finish login

# 3. 准备发布
git flow release start v1.0.0
# 最后的修复...
git flow release finish v1.0.0

# 4. 紧急修复
git flow hotfix start bugfix
# 修复...
git flow hotfix finish bugfix
```

**手动实现 Git Flow**：

```bash
# Main 分支 - 生产代码
# Develop 分支 - 开发代码

# 创建开发分支
git checkout -b develop main

# 功能开发
git checkout -b feature-new develop
# 开发完成
git checkout develop
git merge --no-ff feature-new
git branch -d feature-new

# 发布准备
git checkout -b release-1.0 develop
# 修复版本号等
git checkout main
git merge --no-ff release-1.0
git tag -a v1.0.0 -m "Release v1.0"
git checkout develop
git merge --no-ff release-1.0
git branch -d release-1.0

# Hotfix
git checkout -b hotfix-1.0.1 main
# 修复后
git checkout main
git merge --no-ff hotfix-1.0.1
git tag -a v1.0.1 -m "Hotfix v1.0.1"
git checkout develop
git merge --no-ff hotfix-1.0.1
git branch -d hotfix-1.0.1
```

---

#### 场景 6：多仓库 Fork 协作

```mermaid
flowchart LR
    subgraph Upstream["上游仓库"]
        U["original/repo"]
    end
    
    subgraph Origin["个人仓库"]
        O["yourname/repo"]
    end
    
    subgraph Local["本地仓库"]
        L["~/projects/repo"]
    end
    
    U -->|"Fork"| O
    O -->|"Clone"| L
    L -->|"Push"| O
    O -->|"PR"| U
    
    style Upstream fill:#4ecdc4,color:#fff
    style Origin fill:#ffd700
    style Local fill:#00add8,color:#fff
```

```bash
# 1. Fork 后克隆到本地
git clone git@github.com:yourname/repo.git
cd repo

# 2. 添加上游仓库
git remote add upstream git@github.com:original/repo.git

# 3. 同步上游代码
git fetch upstream
git checkout main
git merge upstream/main
git push origin main

# 4. 开发新功能
git checkout -b feature-new
# 开发并提交
git push origin feature-new

# 5. 在 GitHub 创建 Pull Request 到上游仓库

# 6. 上游更新后，同步到个人仓库
git fetch upstream
git checkout main
git merge upstream/main
git push origin main

# 7. 更新功能分支
git checkout feature-new
git merge main
```

---

#### 场景 7：交互式变基（Rebase）

```mermaid
gitGraph
   commit id: "A"
   commit id: "B"
   branch feature
   commit id: "C"
   commit id: "D"
   checkout main
   commit id: "E"
   commit id: "F"
   checkout feature
   rebase main
   commit id: "C'"
   commit id: "D'"
   checkout main
   merge feature id: "Fast-forward merge"
```

```bash
# 变基前
# A --- B --- C --- D (feature)
#          \
#           E --- F (main)

# 切换到功能分支
git checkout feature

# 变基到 main
git rebase main

# 变基后
# A --- B --- E --- F (main)
#                \
#                 C' --- D' (feature)

# 推送到远程（需要强制推送）
git push --force-with-lease

# 在 main 上合并（可以快进合并）
git checkout main
git merge feature
```

**交互式变基**：

```mermaid
gitGraph
   commit id: "A"
   commit id: "B: add login"
   commit id: "C: fix typo"
   commit id: "D: add profile"
   commit id: "E: refactor"
```

```bash
# 合并最后 4 个提交，修改提交信息
git rebase -i HEAD~4

# 交互式界面示例：
# pick B add login
# reword C fix typo      # 修改提交信息
# squash D add profile   # 合并到上一个提交
# drop E refactor        # 删除这个提交
```

---

#### 场景 8：Git Cherry-Pick

```mermaid
gitGraph
   commit id: "A"
   commit id: "B"
   commit id: "C"
   commit id: "D"
   commit id: "E"
   cherry-pick id: "Pick C"
   commit id: "F"
```

```bash
# Cherry-pick 单个提交
git cherry-pick abc123

# Cherry-pick 多个提交
git cherry-pick abc123 def456

# Cherry-pick 范围
git cherry-pick abc123..def456  # 不包含 abc123
git cherry-pick abc123^..def456 # 包含 abc123

# Cherry-pick 但不提交（用于解决冲突）
git cherry-pick -n abc123
```

---

### 3. 高级使用技巧

#### 3.1 工作区清洁

```mermaid
flowchart TD
    A["工作区状态"] --> B{"需要清理?"}
    B -->|是| C["未跟踪文件"]
    B -->|否| D["正常开发"]
    C --> E{"全部删除?"}
    E -->|是| F["git clean -fd"]
    E -->|否| G["git stash"]
    
    style F fill:#ff6b6b,color:#fff
    style G fill:#ffd700
```

```bash
# 查看将被清理的文件（预览）
git clean -n

# 删除未跟踪文件
git clean -f        # 文件
git clean -fd       # 文件和目录
git clean -fdx      # 包括 .gitignore 中的文件

# 同时清理未跟踪和已暂存
git reset --hard
git clean -fd
```

#### 3.2 Submodule 子模块

```mermaid
flowchart LR
    subgraph MainRepo["主仓库"]
        M1["main project"]
        M2["submodule: lib-a"]
        M3["submodule: common"]
    end
    
    style MainRepo fill:#e3f2fd
```

```bash
# 添加子模块
git submodule add https://github.com/user/repo.git libs/repo

# 克隆包含子模块的仓库
git clone --recurse-submodules main-repo.git

# 或者分步
git clone main-repo.git
git submodule init
git submodule update

# 更新子模块
cd libs/repo
git pull origin main
cd ../..
git add libs/repo
git commit -m "更新子模块"
```

#### 3.3 Git Bisect（二分查找 Bug）

```mermaid
flowchart TD
    A["开始二分查找"] --> B["标记有 Bug 的提交"]
    B --> C["标记正常的提交"]
    C --> D["自动跳转中间提交"]
    D --> E{"这个提交正常?"}
    E -->|是| F["后半部分有问题"]
    E -->|否| G["前半部分有问题"]
    F --> D
    G --> D
    D --> H["找到问题提交"]
    
    style H fill:#ff6b6b,color:#fff
```

```bash
# 开始二分查找
git bisect start

# 标记当前版本有 Bug
git bisect bad

# 标记正常的版本
git bisect good v1.0.0

# Git 自动 checkout 中间版本
# 测试后标记
git bisect good  # 或 git bisect bad

# 重复直到找到
# Git 会输出问题提交

# 结束查找
git bisect reset
```

---

### 4. 常见问题解决方案

| 问题 | 解决方案 | 命令 |
|------|----------|------|
| 误删文件恢复 | reflog | `git reflog` → `git checkout HEAD@{x} -- file` |
| 合并冲突放弃 | abort | `git merge --abort` |
| 提交信息写错 | amend | `git commit --amend -m "新信息"` |
| 忘记加文件 | amend | `git add forgotten.txt` → `git commit --amend` |
| 误删分支恢复 | reflog | `git checkout -b branch-name HEAD@{x}` |
| 查看某行代码历史 | blame | `git blame filename.txt` |
| 大文件传错 | filter-branch | `git filter-branch --tree-filter 'rm file' HEAD` |

---

### 5. 最佳实践总结

```mermaid
gitGraph
   commit id: "Init"
   branch feature
   commit id: "1. Small commit"
   commit id: "2. Working feature"
   commit id: "3. With tests"
   commit id: "4. Docs updated"
   checkout main
   merge feature id: "Clean merge"
   commit id: "Tag: v1.0"
```

#### 提交信息规范（Conventional Commits）

```
<type>(<scope>): <subject>

feat: 添加用户登录功能
fix: 修复登录崩溃问题
docs: 更新 API 文档
style: 格式化代码
refactor: 重构登录模块
test: 添加登录测试
chore: 更新依赖
```

---

### 6. 参考链接

1. [Git Official Website](https://git-scm.com/) — 官方网站
2. [Pro Git Book](https://git-scm.com/book/en/v2) — 权威教程
3. [Oh Shit, Git!?!](https://ohshitgit.com/) — 常见问题解决
4. [GitHub Flow](https://docs.github.com/en/get-started/using-github/github-flow) — GitHub 工作流
5. [Git Flow](https://nvie.com/posts/a-successful-git-branching-model/) — 分支模型
6. [Conventional Commits](https://www.conventionalcommits.org/) — 提交规范

---

> 💡 **一句话总结**：Git 的核心是 **分支管理** + **提交历史**，掌握 `merge`、`rebase`、`cherry-pick` 等技巧，才能游刃有余地应对各种开发场景。

希望这篇带有 GitGraph 可视化的文章对您有所帮助！🎉