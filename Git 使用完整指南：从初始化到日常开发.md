# Git超详细笔记

## 1. Git基础概念

### 1.1 什么是Git
```bash
# Git是一个分布式版本控制系统，用于跟踪文件变化
# 核心概念：工作区、暂存区、仓库

# 查看Git版本
git --version
# git version 2.30.0

# 查看Git帮助
git help
# 显示所有Git命令的帮助信息
```

### 1.2 Git工作流程
```bash
# Git的三种状态和三个工作区域
# 1. 工作目录（Working Directory） - 实际文件
# 2. 暂存区域（Staging Area） - 准备提交的文件
# 3. Git仓库（Git Repository） - 已提交的文件

# 基本工作流程：
# 工作区 → git add → 暂存区 → git commit → 仓库
```

## 2. Git安装和配置

### 2.1 安装Git
```bash
# Windows: 下载Git for Windows
# https://git-scm.com/install/windows

# macOS: brew install git 或下载安装包
# Linux: 
sudo apt-get install git    # Ubuntu/Debian
sudo yum install git        # CentOS/RHEL

# 验证安装
git --version
# git version 2.30.0
```

### 2.2 配置用户信息
```bash
# 设置全局用户名和邮箱（必须配置）
git config --global user.name "你的姓名"
git config --global user.email "你的邮箱@example.com"

# 查看配置
git config --list
# user.name=你的姓名
# user.email=你的邮箱@example.com
# ...

# 设置其他常用配置
git config --global core.editor "vim"          # 设置默认编辑器
git config --global init.defaultBranch "main"  # 设置默认分支名
git config --global color.ui true              # 启用颜色显示

# 查看特定配置
git config user.name
# 你的姓名
```

### 2.3 配置别名
```bash
# 设置命令别名，提高效率
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'

# 现在可以使用别名：
git st  # 相当于 git status
git co -b new-branch  # 相当于 git checkout -b new-branch
```

### 2.4 生成和查看`SSH`密钥
```bash
# 基于 ED25519 算法，生成密钥
ssh-keygen -t ed25519 -C "<注释内容>"

# 查看 ED25519 密钥
cat ~/.ssh/id_ed25519.pub

# 基于 RSA 算法，生成密钥
ssh-keygen -t rsa -C "<注释内容>"

# 查看 RSA 密钥
cat ~/.ssh/id_rsa.pub
```

## 3. 创建和克隆仓库

### 3.1 初始化新仓库
```bash
# 创建项目目录
mkdir my-project
cd my-project

# 初始化Git仓库
git init
# Initialized empty Git repository in /path/to/my-project/.git/

# 查看仓库状态
git status
# On branch main
# No commits yet
# nothing to commit (create/copy files and use "git add" to track)
```

### 3.2 克隆现有仓库
```bash
# 克隆远程仓库到本地
git clone https://github.com/username/repository.git
# Cloning into 'repository'...
# remote: Enumerating objects: 100, done.
# remote: Counting objects: 100% (100/100), done.
# remote: Compressing objects: 100% (80/80), done.
# remote: Total 100 (delta 20), reused 100 (delta 20), pack-reused 0
# Receiving objects: 100% (100/100), 1.5 MiB | 2.3 MiB/s, done.
# Resolving deltas: 100% (20/20), done.

# 克隆到指定目录
git clone https://github.com/username/repository.git my-folder

# 克隆特定分支
git clone -b develop https://github.com/username/repository.git
```

### 3.3 其他操作
```bash
git remote add origin git@github.com:wolf-xxl/...
# 添加远程仓库源，命名为 "origin"
# 指向你的 GitHub 仓库地址

git branch -M main
# 将当前分支重命名为 "main"（替代原来的 "master"）
# -M 表示强制重命名

git push -u origin main
# 将本地 "main" 分支推送到远程 "origin" 仓库
# -u 参数设置上游跟踪，后续可以直接用 git push


```

## 4. 基本文件操作

### 4.1 添加和提交文件
```bash
# 创建新文件
echo "# My Project" > README.md

# 查看状态
git status
# On branch main
# No commits yet
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#     README.md
# nothing added to commit but untracked files present (use "git add" to track)

# 添加文件到暂存区
git add README.md

# 再次查看状态
git status
# On branch main
# No commits yet
# Changes to be committed:
#   (use "git rm --cached <file>..." to unstage)
#     new file:   README.md

# 提交更改
git commit -m "Add README file"
# [main (root-commit) a1b2c3d] Add README file
#  1 file changed, 1 insertion(+)
#  create mode 100644 README.md
```

### 4.2 批量操作文件
```bash
# 添加所有文件（包括新文件和修改的文件）
git add .

# 添加所有修改的文件，但不包括新文件
git add -u

# 添加所有文件（包括新文件和修改的文件）
git add -A

# 添加特定类型的文件
git add *.js
git add src/

# 交互式添加（选择要添加的更改）
git add -p
```

### 4.3 查看和比较更改
```bash
# 查看工作区状态
git status
# On branch main
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git restore <file>..." to discard changes in working directory)
#     modified:   README.md
# no changes added to commit (use "git add" and/or "git commit -a")

# 查看具体更改内容
git diff
# diff --git a/README.md b/README.md
# index 1234567..890abcd 100644
# --- a/README.md
# +++ b/README.md
# @@ -1 +1,2 @@
#  # My Project
# +This is my awesome project.

# 查看已暂存的更改
git diff --staged

# 查看与特定提交的比较
git diff HEAD~1  # 与上一个提交比较
```

## 5. 提交历史

### 5.1 查看提交历史
```bash
# 查看完整提交历史
git log
# commit a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6 (HEAD -> main)
# Author: 你的姓名 <你的邮箱@example.com>
# Date:   Mon Jan 1 10:00:00 2024 +0800
# 
#     Add README file

# 简洁格式显示
git log --oneline
# a1b2c3d Add README file

# 图形化显示分支历史
git log --graph --oneline --all
# * a1b2c3d (HEAD -> main) Add README file

# 显示统计信息
git log --stat
# commit a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6 (HEAD -> main)
# Author: 你的姓名 <你的邮箱@example.com>
# Date:   Mon Jan 1 10:00:00 2024 +0800
# 
#     Add README file
# 
#  README.md | 1 +
#  1 file changed, 1 insertion(+)
```

### 5.2 筛选提交历史
```bash
# 查看最近3次提交
git log -3

# 查看特定作者的提交
git log --author="姓名"

# 查看包含特定关键词的提交
git log --grep="bug"

# 查看特定时间段的提交
git log --since="2024-01-01" --until="2024-01-31"

# 查看特定文件的修改历史
git log README.md

# 显示补丁内容
git log -p
```

## 6. 分支管理

### 6.1 创建和切换分支
```bash
# 查看所有分支
git branch
# * main

# 创建新分支
git branch feature-login

# 切换到新分支
git checkout feature-login
# Switched to branch 'feature-login'

# 创建并切换到新分支（快捷方式）
git checkout -b feature-payment
# Switched to a new branch 'feature-payment'

# 查看所有分支（包括远程分支）
git branch -a
#   feature-login
# * feature-payment
#   main
```

### 6.2 合并分支
```bash
# 切换到主分支
git checkout main
# Switched to branch 'main'

# 合并功能分支
git merge feature-login
# Updating a1b2c3d..e4f5g6h
# Fast-forward
#  login.js | 10 ++++++++++
#  1 file changed, 10 insertions(+)
#  create mode 100644 login.js

# 删除已合并的分支
git branch -d feature-login
# Deleted branch feature-login (was e4f5g6h).

# 强制删除未合并的分支
git branch -D feature-abandoned
```

### 6.3 分支合并冲突解决
```bash
# 模拟冲突场景
# 在两个分支上修改同一个文件的同一行

# 合并时出现冲突
git merge feature-conflict
# Auto-merging config.txt
# CONFLICT (content): Merge conflict in config.txt
# Automatic merge failed; fix conflicts and then commit the result.

# 查看冲突文件状态
git status
# On branch main
# You have unmerged paths.
#   (fix conflicts and run "git commit")
#   (use "git merge --abort" to abort the merge)
# 
# Unmerged paths:
#   (use "git add <file>..." to mark resolution)
#     both modified:   config.txt

# 查看冲突文件内容
cat config.txt
# <<<<<<< HEAD
# database = production_db
# =======
# database = development_db
# >>>>>>> feature-conflict

# 手动解决冲突，编辑文件
# 删除冲突标记，选择或合并内容
vim config.txt  # 编辑为：database = staging_db

# 标记冲突已解决
git add config.txt

# 完成合并提交
git commit -m "Resolve merge conflict in config.txt"
```

## 7. 远程仓库操作

### 7.1 管理远程仓库
```bash
# 查看远程仓库
git remote -v
# origin  https://github.com/username/repository.git (fetch)
# origin  https://github.com/username/repository.git (push)

# 添加远程仓库
git remote add upstream https://github.com/original/repository.git

# 修改远程仓库URL
git remote set-url origin https://github.com/username/new-repository.git

# 移除远程仓库
git remote remove upstream
```

### 7.2 推送和拉取
```bash
# 推送本地分支到远程
git push origin main
# Enumerating objects: 5, done.
# Counting objects: 100% (5/5), done.
# Writing objects: 100% (3/3), 300 bytes | 300.00 KiB/s, done.
# Total 3 (delta 0), reused 0 (delta 0)
# To https://github.com/username/repository.git
#    a1b2c3d..e4f5g6h  main -> main

# 推送并设置上游分支
git push -u origin feature-branch
# 之后可以直接使用 git push

# 拉取远程更改
git pull origin main
# From https://github.com/username/repository
#  * branch            main       -> FETCH_HEAD
# Updating a1b2c3d..e4f5g6h
# Fast-forward
#  README.md | 2 ++
#  1 file changed, 2 insertions(+)

# 获取远程更改但不合并
git fetch origin

# 查看远程分支
git branch -r
# origin/main
# origin/develop
```

## 8. 撤销和重置操作

### 8.1 撤销工作区更改
```bash
# 撤销单个文件的修改（恢复到最新提交的状态）
git checkout -- README.md

# 撤销所有未暂存的修改
git checkout -- .

# 使用restore命令（Git 2.23+）
git restore README.md

# 撤销所有修改，包括暂存区
git reset --hard HEAD
```

### 8.2 撤销暂存区更改
```bash
# 将文件从暂存区移回工作区
git reset HEAD README.md
# Unstaged changes after reset:
# M    README.md

# 使用restore命令
git restore --staged README.md
```

### 8.3 修改提交
```bash
# 修改最后一次提交信息
git commit --amend -m "新的提交信息"

# 添加文件到上次提交
git add forgotten-file.txt
git commit --amend --no-edit

# 修改上次提交的作者信息
git commit --amend --author="新作者 <新作者邮箱>"
```

### 8.4 重置到历史提交
```bash
# 查看提交历史
git log --oneline
# e4f5g6h (HEAD -> main) 添加新功能
# a1b2c3d 初始提交

# 软重置：移动HEAD指针，保留更改在暂存区
git reset --soft a1b2c3d

# 混合重置：移动HEAD指针，保留更改在工作区（默认）
git reset a1b2c3d

# 硬重置：移动HEAD指针，丢弃所有更改
git reset --hard a1b2c3d
# WARNING: 此操作不可逆，谨慎使用！

# 重置单个文件到特定版本
git checkout a1b2c3d -- README.md
```

## 9. 标签管理

### 9.1 创建和管理标签
```bash
# 查看所有标签
git tag
# v1.0.0
# v1.1.0

# 创建轻量标签
git tag v1.2.0

# 创建附注标签（推荐）
git tag -a v1.3.0 -m "Release version 1.3.0"

# 查看标签详情
git show v1.3.0
# tag v1.3.0
# Tagger: 你的姓名 <你的邮箱@example.com>
# Date:   Mon Jan 1 10:00:00 2024 +0800
# 
# Release version 1.3.0
# 
# commit e4f5g6h7i8j9k0l1m2n3o4p5q6r7s8t9
# Author: 你的姓名 <你的邮箱@example.com>
# Date:   Mon Jan 1 09:00:00 2024 +0800
# 
#     添加新功能

# 在特定提交上创建标签
git tag -a v1.2.1 a1b2c3d -m "Patch release"
```

### 9.2 推送和删除标签
```bash
# 推送单个标签到远程
git push origin v1.3.0

# 推送所有标签到远程
git push origin --tags

# 删除本地标签
git tag -d v1.2.0
# Deleted tag 'v1.2.0' (was a1b2c3d)

# 删除远程标签
git push origin --delete v1.2.0
```

## 10. 高级操作

### 10.1 储藏（Stashing）
```bash
# 储藏当前工作区的修改
git stash
# Saved working directory and index state WIP on main: a1b2c3d 初始提交

# 储藏时添加描述
git stash save "正在开发登录功能"

# 查看储藏列表
git stash list
# stash@{0}: On main: 正在开发登录功能
# stash@{1}: WIP on main: a1b2c3d 初始提交

# 应用最近的储藏
git stash apply

# 应用特定储藏
git stash apply stash@{1}

# 应用储藏并删除
git stash pop

# 删除储藏
git stash drop stash@{0}

# 清空所有储藏
git stash clear
```

### 10.2 变基（Rebasing）
```bash
# 变基到主分支
git checkout feature-branch
git rebase main

# 交互式变基（修改历史提交）
git rebase -i HEAD~3
# 会打开编辑器，显示最近3次提交：
# pick a1b2c3d 提交1
# pick e4f5g6h 提交2  
# pick i7j8k9l 提交3
# 
# 可以修改为：
# pick a1b2c3d 提交1
# squash e4f5g6h 提交2  # 合并到前一个提交
# edit i7j8k9l 提交3    # 修改这个提交

# 变基过程中解决冲突
# 1. 编辑冲突文件
# 2. git add 冲突文件
# 3. git rebase --continue

# 中止变基
git rebase --abort
```

### 10.3 子模块（Submodules）
```bash
# 添加子模块
git submodule add https://github.com/username/library.git libs/library
# Cloning into '/path/to/project/libs/library'...
# done.

# 克隆包含子模块的仓库
git clone https://github.com/username/project.git
cd project
git submodule init
git submodule update

# 或者使用递归克隆
git clone --recursive https://github.com/username/project.git

# 更新子模块
git submodule update --remote

# 查看子模块状态
git submodule status
```

## 11. 调试和问题排查

### 11.1 二分查找（Bisect）
```bash
# 开始二分查找
git bisect start

# 标记当前提交为有问题
git bisect bad

# 标记已知的好提交
git bisect good v1.0.0
# Bisecting: 5 revisions left to test after this (roughly 3 steps)
# [a1b2c3d] 提交信息

# 测试当前版本，然后标记好坏
git bisect good  # 如果这个版本没问题
# 或者
git bisect bad   # 如果这个版本有问题

# 重复直到找到问题提交
# 找到第一个坏提交后，结束二分查找
git bisect reset
```

### 11.2 查找问题来源
```bash
# 查看谁最后修改了某行代码
git blame README.md
# a1b2c3d (作者 2024-01-01 10:00:00 +0800 1) # 项目标题
# e4f5g6h (作者 2024-01-02 11:00:00 +0800 2) # 项目描述

# 在提交历史中搜索字符串
git log -S "TODO"  # 搜索添加或删除TODO的提交
git log -G "regex" # 使用正则表达式搜索

# 查看文件的修改历史
git log -p README.md  # 显示该文件的详细修改历史
```

## 12. 工作流和最佳实践

### 12.1 提交信息规范
```bash
# 好的提交信息格式：
# <类型>: <描述>
# 
# <详细说明>
# 
# 常见类型：
# feat: 新功能
# fix: 修复bug
# docs: 文档更新
# style: 代码格式调整
# refactor: 代码重构
# test: 测试相关
# chore: 构建过程或辅助工具变动

# 示例：
git commit -m "feat: 添加用户登录功能

- 实现用户名密码登录
- 添加登录验证
- 更新相关文档"
```

### 12.2 分支命名规范
```bash
# 功能分支: feature/功能描述
git checkout -b feature/user-authentication

# 修复分支: fix/问题描述  
git checkout -b fix/login-bug

# 发布分支: release/版本号
git checkout -b release/v1.2.0

# 热修复分支: hotfix/紧急问题描述
git checkout -b hotfix/critical-security-issue
```

### 12.3 .gitignore文件
```bash
# 创建.gitignore文件
cat > .gitignore << EOF
# 忽略日志文件
*.log

# 忽略依赖目录
node_modules/
vendor/

# 忽略编译输出
dist/
build/
*.class

# 忽略系统文件
.DS_Store
Thumbs.db

# 忽略环境配置文件（包含敏感信息）
.env
config/local.json

# 忽略IDE文件
.vscode/
.idea/
*.swp
*.swo
EOF

# 即使文件已被跟踪，也可以从Git中删除但保留在本地
git rm --cached sensitive-file.txt
```

## 13. 团队协作流程

### 13.1 Fork工作流
```bash
# 1. Fork原仓库到自己的账户
# 2. 克隆自己的Fork
git clone https://github.com/your-username/repository.git

# 3. 添加上游仓库
git remote add upstream https://github.com/original-owner/repository.git

# 4. 创建功能分支
git checkout -b feature-new-feature

# 5. 开发并提交
git add .
git commit -m "feat: 添加新功能"
git push origin feature-new-feature

# 6. 创建Pull Request
# 在GitHub界面操作

# 7. 同步上游更改
git fetch upstream
git merge upstream/main
```

### 13.2 代码审查准备
```bash
# 确保代码是最新的
git fetch origin
git rebase origin/main

# 清理提交历史（交互式变基）
git rebase -i HEAD~3
# 整理提交，使其清晰易懂

# 推送更新
git push origin feature-branch --force-with-lease
# 注意：在共享分支上谨慎使用force push
```

## 14. 常见问题和解决方案

### 14.1 恢复误删的文件
```bash
# 查看删除记录
git log --diff-filter=D --summary
# commit e4f5g6h7i8j9k0l1m2n3o4p5q6r7s8t9
# Author: 你的姓名 <你的邮箱@example.com>
# Date:   Mon Jan 1 11:00:00 2024 +0800
# 
#     删除旧文件
# 
# delete mode 100644 old-file.txt

# 恢复文件
git checkout e4f5g6h7i8j9k0l1m2n3o4p5q6r7s8t9^ -- old-file.txt
# ^ 表示该提交的父提交
```

### 14.2 处理大文件问题
```bash
# 查看仓库大小
git count-objects -vH
# count: 10
# size: 256.00 KiB
# in-pack: 100
# packs: 1
# size-pack: 1.50 MiB

# 从历史中彻底删除大文件（使用BFG Repo Cleaner或git filter-branch）
# 注意：这会重写历史，影响所有协作者

# 使用git filter-branch
git filter-branch --tree-filter 'rm -f large-file.zip' HEAD

# 清理和优化仓库
git reflog expire --expire=now --all
git gc --prune=now --aggressive
```

### 14.3 凭证管理
```bash
# 缓存凭证（默认15分钟）
git config --global credential.helper cache

# 缓存1小时
git config --global credential.helper 'cache --timeout=3600'

# 使用系统密钥链存储（macOS）
git config --global credential.helper osxkeychain

# 使用Windows凭据管理器
git config --global credential.helper wincred

# 使用Linux密钥环
git config --global credential.helper gnome-keyring
```

## 15. Git钩子（Hooks）

### 15.1 使用Git钩子
```bash
# 查看可用的钩子
ls .git/hooks/
# applypatch-msg.sample commit-msg.sample post-update.sample pre-applypatch.sample pre-commit.sample pre-push.sample pre-rebase.sample prepare-commit-msg.sample update.sample

# 创建pre-commit钩子
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/sh
# 在提交前运行代码检查

echo "Running tests before commit..."
npm test
if [ $? -ne 0 ]; then
    echo "Tests failed! Commit aborted."
    exit 1
fi
EOF

# 使钩子可执行
chmod +x .git/hooks/pre-commit
```

### 15.2 客户端钩子示例
```bash
# pre-commit: 提交前检查
# 示例：检查代码格式
#!/bin/sh
echo "Checking code format..."
npx prettier --check src/

# commit-msg: 检查提交信息格式
#!/bin/sh
MSG_FILE=$1
MSG=$(cat $MSG_FILE)

if ! echo "$MSG" | grep -qE "^(feat|fix|docs|style|refactor|test|chore): "; then
    echo "ERROR: Commit message must start with feat|fix|docs|style|refactor|test|chore:"
    exit 1
fi

# pre-push: 推送前检查
#!/bin/sh
echo "Running full test suite before push..."
npm run test:all
```

## 16. Git配置优化

### 16.1 性能优化配置
```bash
# 启用文件系统监视（提高大型仓库性能）
git config --global core.fsmonitor true

# 设置压缩级别
git config --global core.compression 9

# 启用提交图（提高日志性能）
git config --global core.commitGraph true
git config --global gc.writeCommitGraph true

# 配置大文件阈值
git config --global core.bigFileThreshold 100m
```

### 16.2 安全和验证配置
```bash
# 启用提交签名（需要GPG密钥）
git config --global commit.gpgsign true
git config --global user.signingkey YOUR_GPG_KEY_ID

# 推送时验证
git config --global push.followTags true
git config --global push.default simple

# 拉取时 rebase
git config --global pull.rebase true
```
