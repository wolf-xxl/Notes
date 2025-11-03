
## Git 简介

Git 是一个分布式版本控制系统，用于跟踪文件变更并协调多人协作开发。

## 核心概念

· 仓库 (Repository): 存储项目所有版本数据的地方
· 提交 (Commit): 项目的一个版本快照
· 分支 (Branch): 独立开发线
· 远程仓库 (Remote): 存储在服务器上的仓库副本

## 安装与配置

### 安装 Git

```bash
# Ubuntu/Debian
sudo apt-get install git

# macOS
brew install git

# Windows
# 下载 Git for Windows: https://git-scm.com/
```

### 基础配置

```bash
# 设置用户名和邮箱
git config --global user.name "你的姓名"
git config --global user.email "你的邮箱@example.com"

# 设置默认编辑器
git config --global core.editor "vim"

# 查看配置
git config --list
```

## 基础操作

### 初始化仓库

```bash
# 创建新仓库
git init 项目名称
cd 项目名称

# 克隆现有仓库
git clone https://github.com/用户名/仓库名.git
```

### 基本工作流

```bash
# 查看仓库状态
git status

# 添加文件到暂存区
git add 文件名              # 添加特定文件
git add .                  # 添加所有文件
git add *.js               # 添加所有js文件

# 提交更改
git commit -m "提交说明"

# 查看提交历史
git log
git log --oneline          # 简洁显示
git log --graph            # 图形化显示分支
```

### 查看差异

```bash
# 查看工作区与暂存区的差异
git diff

# 查看暂存区与最新提交的差异
git diff --staged

# 查看两次提交之间的差异
git diff commit1 commit2

# 查看特定文件的差异
git diff 文件名
```

### 撤销操作

```bash
# 撤销工作区的修改
git checkout -- 文件名

# 撤销暂存区的文件（取消add）
git reset HEAD 文件名

# 修改最后一次提交
git commit --amend -m "新的提交信息"

# 回退到指定提交
git reset --hard commit_id
```

## 分支管理

### 基础分支操作

```bash
# 查看分支
git branch                 # 本地分支
git branch -r             # 远程分支
git branch -a             # 所有分支

# 创建分支
git branch 分支名
git checkout -b 分支名     # 创建并切换到新分支

# 切换分支
git checkout 分支名
git switch 分支名          # 新版本推荐

# 合并分支
git checkout main
git merge 分支名

# 删除分支
git branch -d 分支名       # 安全删除
git branch -D 分支名       # 强制删除
```

### 分支合并策略

```bash
# 普通合并（保留分支历史）
git merge feature-branch

# 变基合并（线性历史）
git rebase main
git checkout main
git merge feature-branch

# 解决合并冲突后
git add .
git commit -m "解决合并冲突"
```

## 远程仓库

### 远程仓库操作

```bash
# 查看远程仓库
git remote -v

# 添加远程仓库
git remote add origin https://github.com/用户名/仓库名.git

# 推送代码
git push -u origin main          # 首次推送
git push origin 分支名           # 推送特定分支

# 拉取代码
git pull origin main            # 拉取并合并
git fetch origin               # 只下载不合并
git merge origin/main          # 合并下载的代码
```

### 同步工作流

```bash
# 标准同步流程
git fetch origin              # 获取远程更新
git status                   # 查看状态
git merge origin/main        # 合并到当前分支

# 或者使用 pull（fetch + merge）
git pull origin main

# 使用 rebase 同步
git pull --rebase origin main
```

## 解决冲突

### 冲突处理流程

1. 识别冲突

```bash
git status
# 会显示冲突文件列表
```

2. 编辑冲突文件
   冲突标记示例：

```javascript
<<<<<<< HEAD
// 当前分支的代码
console.log("本地修改");
=======
// 远程分支的代码
console.log("远程修改");
>>>>>>> branch-name
```

3. 解决冲突

· 手动编辑文件，选择保留的代码
· 删除冲突标记 (<<<<<<<, =======, >>>>>>>)

4. 完成解决

```bash
git add 冲突文件
git commit -m "解决合并冲突"
```

5. 高级冲突解决

```bash
# 使用图形化工具解决冲突
git mergetool

# 中止合并
git merge --abort
git rebase --abort

# 跳过当前冲突（慎用）
git rebase --skip
```

## 高级技巧

### 储藏更改

```bash
# 临时保存工作区
git stash
git stash save "储藏说明"

# 查看储藏列表
git stash list

# 恢复储藏
git stash pop              # 恢复并删除
git stash apply           # 恢复但不删除

# 删除储藏
git stash drop stash@{0}
```

### 标签管理

```bash
# 创建标签
git tag v1.0.0
git tag -a v1.0.0 -m "版本发布说明"

# 推送标签
git push origin v1.0.0
git push origin --tags    # 推送所有标签

# 删除标签
git tag -d v1.0.0
git push origin --delete v1.0.0
```

### 子模块

```bash
# 添加子模块
git submodule add https://github.com/用户名/仓库.git

# 初始化子模块
git submodule init
git submodule update

# 更新子模块
git submodule update --remote
```

### 常用工作流

#### 功能分支工作流

```bash
# 1. 基于main创建功能分支
git checkout -b feature/new-feature

# 2. 开发并提交
git add .
git commit -m "实现新功能"

# 3. 同步主分支
git checkout main
git pull origin main
git checkout feature/new-feature
git rebase main

# 4. 推送并创建Pull Request
git push origin feature/new-feature
```

#### 紧急修复工作流

```bash
# 1. 从main创建修复分支
git checkout -b hotfix/urgent-fix

# 2. 修复并提交
git add .
git commit -m "修复紧急问题"

# 3. 合并到main
git checkout main
git merge hotfix/urgent-fix
git push origin main

# 4. 删除修复分支
git branch -d hotfix/urgent-fix
```

### 日常检查清单

```bash
# 开始工作前
git status
git pull origin main

# 提交更改前
git diff
git status

# 推送前
git log --oneline -5
git push origin 分支名
```

### 实用别名配置

```bash
# 添加到 ~/.gitconfig
[alias]
    co = checkout
    ci = commit
    st = status
    br = branch
    hist = log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short
    type = cat-file -t
    dump = cat-file -p
```

