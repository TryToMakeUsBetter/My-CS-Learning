# Git的使用

## 基础结构

### Git的三个区域
- **工作区（Working Directory）**: 实际编辑文件的地方
- **暂存区（Staging Area/Index）**: 临时存储即将提交的修改
- **仓库（Repository）**: 存储所有版本历史记录的地方
  - 本地仓库（Local Repository）
  - 远程仓库（Remote Repository）

### Git的四种状态
- **未跟踪（Untracked）**: 新文件，Git还未开始跟踪
- **未修改（Unmodified）**: 文件已被跟踪，但未修改
- **已修改（Modified）**: 文件已修改，但未添加到暂存区
- **已暂存（Staged）**: 文件已添加到暂存区，准备提交

### 基本工作流程
```
工作区 --git add--> 暂存区 --git commit--> 本地仓库 --git push--> 远程仓库
远程仓库 --git fetch--> 本地仓库 --git merge--> 工作区
远程仓库 --git pull (fetch + merge)--> 工作区
```

## Git常识

### 版本控制的意义
- **历史追踪**: 记录每次修改的内容和原因
- **协作开发**: 多人同时开发，合并各自的修改
- **版本回退**: 可以回到任意历史版本
- **分支管理**: 并行开发不同功能，互不干扰

### 提交信息规范
常用的提交信息格式：
```
<type>: <subject>

<body>

<footer>
```

**类型（type）**：
- `feat`: 新功能
- `fix`: 修复bug
- `docs`: 文档修改
- `style`: 代码格式修改（不影响代码运行）
- `refactor`: 重构代码
- `test`: 测试用例修改
- `chore`: 构建过程或辅助工具的变动

**示例**：
```
feat: 添加用户登录功能

实现了基于JWT的用户认证系统
- 支持用户名密码登录
- 支持token自动刷新

Closes #123
```

### 分支策略

**Git Flow**：
- `master/main`: 主分支，保持稳定可发布状态
- `develop`: 开发分支，最新的开发进度
- `feature/*`: 功能分支，从develop分出
- `release/*`: 发布分支，准备发布的版本
- `hotfix/*`: 热修复分支，紧急修复线上bug

**GitHub Flow**（更简单）：
- `main`: 主分支，随时可部署
- `feature/*`: 功能分支，开发完成后通过PR合并回main

### .gitignore文件
用于指定Git应该忽略的文件和目录：
```gitignore
# 依赖目录
node_modules/
vendor/

# 构建产物
dist/
build/
*.o
*.exe

# 日志文件
*.log
logs/

# 环境配置
.env
.env.local

# IDE配置
.vscode/
.idea/
*.swp

# 操作系统文件
.DS_Store
Thumbs.db
```

## 常用指令

### 初始化与配置

#### 初始化仓库
```bash
git init                    # 在当前目录创建新的Git仓库
git clone <url>             # 克隆远程仓库到本地
git clone <url> <dirname>   # 克隆并指定目录名
```

#### 配置用户信息
```bash
# 全局配置（所有仓库生效）
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# 当前仓库配置
git config user.name "Your Name"
git config user.email "your.email@example.com"

# 查看配置
git config --list
git config user.name
```

#### 其他配置
```bash
# 设置默认编辑器
git config --global core.editor "vim"

# 设置默认分支名
git config --global init.defaultBranch main

# 配置别名
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
```

### 基本操作

#### 查看状态
```bash
git status              # 查看工作区状态
git status -s           # 简洁模式
git diff                # 查看工作区与暂存区的差异
git diff --staged       # 查看暂存区与最后一次提交的差异
git diff HEAD           # 查看工作区与最后一次提交的差异
git diff <branch1> <branch2>  # 比较两个分支
```

#### 添加文件到暂存区
```bash
git add <file>          # 添加指定文件
git add .               # 添加所有修改（当前目录及子目录）
git add -A              # 添加所有修改（整个仓库）
git add -u              # 只添加已跟踪文件的修改（不包括新文件）
git add -p              # 交互式添加（分块选择）
```

#### 提交
```bash
git commit -m "message"           # 提交暂存区的修改
git commit -am "message"          # 添加所有已跟踪文件并提交
git commit --amend                # 修改最后一次提交
git commit --amend --no-edit      # 修改最后一次提交但不改消息
```

#### 撤销操作
```bash
# 撤销工作区的修改
git checkout -- <file>            # 旧版本
git restore <file>                # 新版本（推荐）

# 撤销暂存区的文件（保留工作区修改）
git reset HEAD <file>             # 旧版本
git restore --staged <file>       # 新版本（推荐）

# 撤销提交
git reset --soft HEAD~1           # 撤销提交，保留暂存区和工作区
git reset --mixed HEAD~1          # 撤销提交和暂存，保留工作区（默认）
git reset --hard HEAD~1           # 撤销提交、暂存和工作区的修改（危险）
git reset --hard <commit-hash>    # 回退到指定提交

# 反做某次提交（生成新提交）
git revert <commit-hash>          # 安全的撤销方式
```

#### 删除与重命名
```bash
git rm <file>                     # 删除文件并暂存
git rm --cached <file>            # 从Git中删除但保留本地文件
git mv <old-name> <new-name>      # 重命名文件
```

### 分支管理

#### 查看分支
```bash
git branch                        # 查看本地分支
git branch -r                     # 查看远程分支
git branch -a                     # 查看所有分支
git branch -v                     # 查看各分支最后一次提交
git branch --merged               # 查看已合并到当前分支的分支
git branch --no-merged            # 查看未合并到当前分支的分支
```

#### 创建与切换分支
```bash
git branch <branch-name>          # 创建分支
git checkout <branch-name>        # 切换分支（旧版本）
git switch <branch-name>          # 切换分支（新版本，推荐）
git checkout -b <branch-name>     # 创建并切换分支（旧版本）
git switch -c <branch-name>       # 创建并切换分支（新版本，推荐）
git checkout -b <branch> <remote>/<branch>  # 基于远程分支创建本地分支
```

#### 合并分支
```bash
git merge <branch-name>           # 合并指定分支到当前分支
git merge --no-ff <branch>        # 禁用快进合并（保留分支历史）
git merge --squash <branch>       # 压缩合并（所有提交合并为一个）
git merge --abort                 # 取消合并（冲突时）
```

#### 变基（Rebase）
```bash
git rebase <branch>               # 将当前分支变基到指定分支
git rebase -i HEAD~3              # 交互式变基最近3个提交
git rebase --continue             # 解决冲突后继续
git rebase --abort                # 取消变基
```

**merge vs rebase**：
- `merge`: 保留完整历史，会产生合并提交
- `rebase`: 线性历史，更清晰，但会改写历史（不要在公共分支使用）

#### 删除分支
```bash
git branch -d <branch-name>       # 删除已合并的分支
git branch -D <branch-name>       # 强制删除分支
git push origin --delete <branch> # 删除远程分支
git branch --set-upstream-to=origin/release local_release # 切换上游远端分支
```

### 远程仓库

#### 查看远程仓库
```bash
git remote                        # 查看远程仓库名称
git remote -v                     # 查看远程仓库详细信息
git remote show <remote>          # 查看指定远程仓库信息
```

#### 添加与删除远程仓库
```bash
git remote add <name> <url>       # 添加远程仓库
git remote rename <old> <new>     # 重命名远程仓库
git remote remove <name>          # 删除远程仓库
git remote set-url <name> <url>   # 修改远程仓库地址
```

#### 拉取与推送
```bash
# 拉取
git fetch <remote>                # 拉取远程仓库的所有分支
git fetch <remote> <branch>       # 拉取指定分支
git pull <remote> <branch>        # 拉取并合并（fetch + merge）
git pull --rebase                 # 拉取并变基（fetch + rebase）

# 推送
git push <remote> <branch>        # 推送到远程分支
git push -u origin <branch>       # 推送并设置上游分支
git push origin --delete <branch> # 删除远程分支
git push --force                  # 强制推送（危险，会覆盖远程历史）
git push --force-with-lease       # 更安全的强制推送
git push --tags                   # 推送标签
```

#### 跟踪分支
```bash
# 设置上游分支
git branch -u <remote>/<branch>
git push -u origin <branch>

# 查看跟踪关系
git branch -vv
```

### 查看历史

#### 查看提交历史
```bash
git log                           # 查看提交历史
git log --oneline                 # 每个提交显示一行
git log --graph                   # 图形化显示分支历史
git log --all                     # 显示所有分支
git log --decorate                # 显示分支和标签
git log -p                        # 显示每次提交的差异
git log -<n>                      # 显示最近n次提交
git log --since="2 weeks ago"     # 指定时间范围
git log --author="name"           # 指定作者
git log --grep="keyword"          # 搜索提交信息
git log <file>                    # 查看文件的提交历史
git log --follow <file>           # 查看文件历史（包括重命名）

# 常用组合
git log --oneline --graph --all --decorate
```

#### 查看引用日志
```bash
git reflog                        # 查看所有操作历史（包括reset等）
git reflog show <branch>          # 查看指定分支的操作历史
```

#### 查看具体提交
```bash
git show <commit-hash>            # 查看提交详情
git show <commit>:<file>          # 查看某次提交中的文件内容
```

### 标签管理

#### 创建标签
```bash
git tag                           # 列出所有标签
git tag <tag-name>                # 创建轻量标签
git tag -a <tag-name> -m "msg"    # 创建附注标签
git tag -a <tag-name> <commit>    # 给指定提交打标签
```

#### 查看与删除标签
```bash
git show <tag-name>               # 查看标签信息
git tag -d <tag-name>             # 删除本地标签
git push origin --delete <tag>    # 删除远程标签
```

#### 推送标签
```bash
git push origin <tag-name>        # 推送指定标签
git push origin --tags            # 推送所有标签
```

### 储藏（Stash）

当需要切换分支但不想提交当前工作时使用：

```bash
git stash                         # 储藏当前修改
git stash save "message"          # 储藏并添加说明
git stash list                    # 查看储藏列表
git stash show                    # 查看最新储藏的内容
git stash show -p                 # 查看详细差异
git stash apply                   # 应用最新储藏（不删除）
git stash apply stash@{n}         # 应用指定储藏
git stash pop                     # 应用并删除最新储藏
git stash drop                    # 删除最新储藏
git stash drop stash@{n}          # 删除指定储藏
git stash clear                   # 清空所有储藏
```

### 子模块（Submodule）

```bash
# 添加子模块
git submodule add <url> <path>

# 克隆包含子模块的仓库
git clone --recursive <url>
# 或者先克隆再初始化
git clone <url>
git submodule init
git submodule update

# 更新子模块
git submodule update --remote

# 查看子模块状态
git submodule status
```

### 其他实用命令

#### 查找与搜索
```bash
git grep "keyword"                # 在仓库中搜索关键字
git grep "keyword" <branch>       # 在指定分支搜索
git blame <file>                  # 查看文件每行的最后修改者
git blame -L 10,20 <file>         # 查看指定行范围
```

#### 清理
```bash
git clean -n                      # 预览要删除的未跟踪文件
git clean -f                      # 删除未跟踪的文件
git clean -fd                     # 删除未跟踪的文件和目录
git clean -fX                     # 只删除被忽略的文件
git gc                            # 垃圾回收，优化仓库
```

#### Cherry-pick
```bash
git cherry-pick <commit-hash>     # 应用指定提交到当前分支
git cherry-pick <commit1> <commit2>  # 应用多个提交
git cherry-pick <start-commit>..<end-commit>  # 连续应用多个提交（不包含start-commit）
git cherry-pick <start-commit>^..<end-commit>  # 连续应用多个提交（包含start-commit）
git cherry-pick --continue        # 解决冲突后继续
git cherry-pick --abort           # 取消cherry-pick
```

## 常见场景

### 场景1：撤销已推送的提交
```bash
# 方法1：使用revert（推荐，不改变历史）
git revert <commit-hash>
git push origin <branch>

# 方法2：使用reset（需要强制推送，慎用）
git reset --hard <commit-hash>
git push --force-with-lease origin <branch>
```

### 场景2：合并多个提交
```bash
# 方法1：交互式rebase
git rebase -i HEAD~3              # 合并最近3个提交
# 在编辑器中将除第一个外的pick改为squash

# 方法2：reset后重新提交
git reset --soft HEAD~3
git commit -m "combined message"
```

### 场景3：修改历史提交信息
```bash
# 修改最后一次提交
git commit --amend

# 修改更早的提交
git rebase -i HEAD~3
# 将要修改的提交的pick改为edit
# 修改后执行：
git commit --amend
git rebase --continue
```

### 场景4：解决合并冲突
```bash
# 1. 尝试合并
git merge <branch>

# 2. 如果有冲突，查看冲突文件
git status

# 3. 手动编辑冲突文件，保留需要的内容
# 冲突标记：
# <<<<<<< HEAD
# 当前分支的内容
# =======
# 要合并分支的内容
# >>>>>>> branch-name

# 4. 标记为已解决
git add <resolved-files>

# 5. 完成合并
git commit
```

### 场景5：同步fork的仓库
```bash
# 1. 添加上游仓库
git remote add upstream <original-repo-url>

# 2. 拉取上游更新
git fetch upstream

# 3. 合并到本地主分支
git checkout main
git merge upstream/main

# 4. 推送到自己的远程仓库
git push origin main
```

### 场景6：临时切换分支
```bash
# 储藏当前工作
git stash

# 切换分支进行其他工作
git checkout other-branch
# ... 完成工作 ...

# 返回原分支
git checkout original-branch

# 恢复工作
git stash pop
```

## 最佳实践

1. **频繁提交，精心推送**：本地可以频繁提交，推送前整理提交历史
2. **有意义的提交信息**：清楚描述"做了什么"和"为什么这样做"
3. **小而专注的提交**：每次提交只做一件事，便于回溯和回退
4. **不要提交生成的文件**：使用.gitignore排除构建产物、依赖包等
5. **拉取前先提交**：避免本地修改与远程修改冲突
6. **使用分支开发**：不直接在main分支开发，使用功能分支
7. **代码审查**：通过Pull Request进行代码审查再合并
8. **避免force push**：除非确实需要，否则不要强制推送到公共分支
9. **定期同步远程**：经常fetch/pull保持与远程同步
10. **备份重要分支**：进行危险操作前先创建备份分支

## 常用别名配置

在 `~/.gitconfig` 中添加：
```ini
[alias]
    st = status
    co = checkout
    br = branch
    ci = commit
    df = diff
    lg = log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
    last = log -1 HEAD
    unstage = reset HEAD --
    visual = log --graph --oneline --all --decorate
    amend = commit --amend --no-edit
    undo = reset --soft HEAD~1
```

使用方式：
```bash
git st        # 相当于 git status
git lg        # 美化的日志显示
git visual    # 可视化的分支图
```


