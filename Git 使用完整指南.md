---
tags:
  - Git
date: 2025-11-03
---

## 1. Git 初始化配置

### 1.1 安装与基础配置
```bash
# 配置用户信息（必须设置）
git config --global user.name "你的用户名"
git config --global user.email "你的邮箱"

# 查看配置
git config --list

# 设置默认编辑器（可选）
git config --global core.editor "vim"
```

**⚠️ 易错点**：忘记配置用户信息会导致提交时无法识别作者，团队协作时会出现问题。

## 2. SSH 密钥配置与管理

### 2.1 生成 SSH 密钥
```bash
# 生成新的 SSH 密钥（推荐使用 ED25519 算法）
ssh-keygen -t ed25519 -C "your_email@example.com"

# 或者使用 RSA 算法（兼容旧系统）
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# 指定密钥保存路径
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_work -C "work_email@company.com"
```

**生成过程中的选项**：
- 密钥保存路径：按 Enter 使用默认路径
- 密码短语：建议设置增强安全性
- 确认密码短语：再次输入相同密码

### 2.2 管理多个 SSH 密钥
```bash
# 查看现有密钥
ls -al ~/.ssh

# 启动 SSH 代理
eval "$(ssh-agent -s)"

# 将密钥添加到 SSH 代理
ssh-add ~/.ssh/id_ed25519
ssh-add ~/.ssh/id_ed25519_work

# 查看已添加的密钥
ssh-add -l

# 删除缓存的密钥
ssh-add -D
```

### 2.3 配置多账户 SSH
创建或编辑 `~/.ssh/config` 文件：
```
# 个人 GitHub 账户
Host github-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_personal

# 工作 GitHub 账户
Host github-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_work

# 公司 GitLab
Host gitlab-company
    HostName gitlab.company.com
    User git
    IdentityFile ~/.ssh/id_rsa_company
```

### 2.4 测试 SSH 连接
```bash
# 测试连接
ssh -T git@github.com

# 使用特定配置测试
ssh -T github-personal
ssh -T github-work
```

**✅ 成功提示**：`Hi username! You've successfully authenticated...`

### 2.5 将公钥添加到远程仓库
```bash
# 复制公钥到剪贴板
cat ~/.ssh/id_ed25519.pub | pbcopy  # macOS
cat ~/.ssh/id_ed25519.pub | clip    # Windows
cat ~/.ssh/id_ed25519.pub | xsel --clipboard --input  # Linux

# 或者手动显示并复制
cat ~/.ssh/id_ed25519.pub
```

**添加位置**：
- GitHub: Settings → SSH and GPG keys → New SSH key
- GitLab: Preferences → SSH Keys
- 其他平台类似位置

**⚠️ 易错点**：
- 复制私钥而不是公钥（私钥绝不能共享）
- 公钥格式错误（确保完整复制，没有多余空格）
- 忘记在远程仓库添加公钥

## 3. 仓库初始化与基础操作

### 3.1 使用 SSH 克隆仓库
```bash
# 使用 SSH URL 克隆（替代 HTTPS）
git clone git@github.com:username/repository.git

# 使用配置的 SSH 主机名克隆
git clone github-personal:username/repository.git

# 与 HTTPS URL 对比
git clone https://github.com/username/repository.git  # 需要输入密码或 token
```

### 3.2 修改远程仓库 URL
```bash
# 查看当前远程仓库 URL
git remote -v

# 从 HTTPS 切换到 SSH
git remote set-url origin git@github.com:username/repository.git

# 添加多个远程仓库
git remote add personal git@github.com:personal/repo.git
git remote add work git@github.com:company/repo.git
```

## 4. 分支管理

### 4.1 分支基础操作
```bash
# 查看分支
git branch                   # 本地分支
git branch -r               # 远程分支
git branch -a               # 所有分支

# 创建分支
git branch feature-branch
git checkout -b feature-branch  # 创建并切换

# 切换分支
git checkout main
git switch main              # 新版本推荐方式

# 删除分支
git branch -d feature-branch # 安全删除（已合并）
git branch -D feature-branch # 强制删除（未合并）
```

### 4.2 分支合并策略

#### 4.2.1 Fast-Forward 合并
```bash
# 当分支线性发展时使用
git checkout main
git merge feature-branch
```

#### 4.2.2 三方合并
```bash
# 当分支有分叉时
git checkout main
git merge --no-ff feature-branch  # 保留合并记录
```

**⚠️ 易错点**：
- 在错误的分支上进行开发
- 忘记切换分支直接提交
- 删除未合并的分支导致代码丢失

## 5. 远程仓库操作

### 5.1 远程仓库配置
```bash
# 添加远程仓库（使用 SSH）
git remote add origin git@github.com:username/repo.git

# 查看远程仓库
git remote -v

# 推送分支（SSH 无需重复输入密码）
git push -u origin main     # 首次推送设置上游分支
git push origin feature-branch

# 拉取更新
git pull origin main        # 拉取并合并
git fetch origin           # 只下载不合并
```

### 5.2 跟踪远程分支
```bash
# 创建本地分支跟踪远程分支
git checkout -b feature origin/feature
git branch --set-upstream-to=origin/feature feature
```

**⚠️ 易错点**：
- 忘记 `git pull` 导致本地版本落后
- 推送前未解决冲突
- 在错误的上游分支进行推送

## 6. 代码回退与撤销

### 6.1 撤销工作区修改
```bash
# 撤销未暂存的修改
git checkout -- filename.txt

# 撤销所有未暂存的修改
git checkout -- .

# 新版本推荐方式
git restore filename.txt
```

### 6.2 撤销暂存区文件
```bash
# 将文件从暂存区移回工作区
git reset HEAD filename.txt

# 新版本推荐方式
git restore --staged filename.txt
```

### 6.3 版本回退
```bash
# 回退到指定提交
git reset --hard commit_id     # 彻底回退，丢弃所有修改
git reset --soft commit_id     # 回退提交，保留修改到暂存区
git reset --mixed commit_id    # 默认，回退提交，保留修改到工作区

# 回退到上一个版本
git reset --hard HEAD^
git reset --hard HEAD~1

# 恢复已删除的提交（慎用）
git reflog                    # 查看操作记录
git reset --hard commit_id    # 恢复到指定提交
```

**⚠️ 易错点**：
- 使用 `git reset --hard` 前未备份重要修改
- 对已推送到远程的提交进行 reset 操作
- 误删未推送的分支

## 7. 冲突解决

### 7.1 合并冲突处理
```bash
# 合并时出现冲突
git merge feature-branch

# 查看冲突文件
git status

# 手动解决冲突后
git add resolved-file.txt
git commit -m "Resolve merge conflicts"
```

### 7.2 变基冲突处理
```bash
# 变基操作
git rebase main

# 解决冲突后继续变基
git add resolved-file.txt
git rebase --continue

# 放弃变基
git rebase --abort
```

**冲突文件示例**：
```
<<<<<<< HEAD
当前分支的代码
=======
要合并分支的代码
>>>>>>> feature-branch
```

**⚠️ 易错点**：
- 未完全理解冲突内容就进行解决
- 忘记标记冲突已解决（git add）
- 在共享分支上使用 rebase

## 8. 高级操作

### 8.1 储藏更改
```bash
# 临时保存工作区修改
git stash                    # 储藏所有修改
git stash push -m "描述信息" # 带描述的储藏
git stash list              # 查看储藏列表
git stash pop               # 恢复最新储藏并删除
git stash apply             # 恢复但不删除
git stash drop              # 删除储藏
```

### 8.2 标签管理
```bash
# 创建标签
git tag v1.0.0              # 轻量标签
git tag -a v1.0.0 -m "版本发布" # 注解标签

# 推送标签
git push origin v1.0.0
git push origin --tags      # 推送所有标签
```

## 9. SSH 密钥故障排除

### 9.1 常见问题解决
```bash
# 1. 检查 SSH 密钥权限
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_*

# 2. 确保 SSH 代理运行
eval "$(ssh-agent -s)"

# 3. 重新添加密钥
ssh-add ~/.ssh/your_private_key

# 4. 测试具体连接
ssh -vT git@github.com  # 详细输出，用于调试

# 5. 检查配置文件语法
ssh -T github-personal -v  # 测试特定主机配置
```

### 9.2 多账户配置验证
```bash
# 验证不同账户使用正确密钥
GIT_SSH_COMMAND="ssh -v" git clone github-personal:user/repo.git
GIT_SSH_COMMAND="ssh -v" git clone github-work:company/repo.git
```

## 10. 最佳实践与工作流

### 10.1 提交规范
```
feat: 新功能
fix: 修复bug
docs: 文档更新
style: 代码格式调整
refactor: 重构
test: 测试相关
chore: 构建过程或辅助工具变动
```

### 10.2 分支命名规范
```
feature/user-authentication   # 新功能
bugfix/login-error           # bug修复
hotfix/critical-security     # 紧急修复
release/v1.2.0              # 发布分支
```

### 10.3 使用 SSH 的日常工作流
```bash
# 1. 开始新功能（使用 SSH 克隆如果尚未克隆）
git clone git@github.com:username/repo.git
cd repo

# 2. 创建功能分支
git checkout -b feature/new-feature

# 3. 开发并提交
git add .
git commit -m "feat: add new feature"

# 4. 同步主分支
git checkout main
git pull origin main

# 5. 合并功能分支
git checkout feature/new-feature
git rebase main

# 6. 推送并创建 PR（无需输入密码）
git push origin feature/new-feature
```

## 11. 安全注意事项

### 11.1 SSH 密钥安全
- **私钥保护**：私钥文件权限应为 600，绝不能共享
- **密码短语**：为密钥设置强密码短语
- **定期更换**：建议每年更换一次 SSH 密钥
- **撤销泄露密钥**：如果怀疑密钥泄露，立即从所有平台撤销

### 11.2 多环境管理
```bash
# 为不同环境使用不同密钥
# 开发环境：较低安全要求的密钥
# 生产环境：使用硬件安全模块（HSM）或更高安全级别的密钥
```

## 12. 重要提醒

### ⚠️ 绝对不要做的操作：
1. **不要**在公共分支上使用 `git reset --hard`
2. **不要**强制推送 (`git push -f`) 到共享分支
3. **不要**在未理解后果的情况下使用 `git rebase` 公共分支
4. **不要**提交大文件到 Git 仓库
5. **不要**忘记 `.gitignore` 文件配置
6. **不要**共享 SSH 私钥
7. **不要**将私钥提交到代码仓库

### ✅ 推荐做法：
1. 使用 SSH 密钥替代密码认证
2. 为不同账户使用不同密钥
3. 为 SSH 密钥设置强密码短语
4. 使用 SSH 代理管理密钥
5. 定期更新 SSH 密钥
6. 频繁提交，小步提交
7. 编写清晰的提交信息
8. 定期同步远程仓库
9. 使用分支进行功能开发
10. 代码审查后再合并到主分支