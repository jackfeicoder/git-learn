# Git 基础

## 1. 核心概念

- **工作区（Working Directory）**：写代码、改文件的地方。
- **暂存区（Staging Area / Index）**：保存改动快照的地方。执行 `git add .` 会把工作区的改动提交到暂存区。
  - Git 开始监控文件的变动，使用 `git diff` 可以查看工作区与暂存区的差异。
- **本地仓库（Local Repository）**：保存项目所有历史版本的地方。执行 `git commit` 会把暂存区的改动正式归档到本地仓库。
- **远程仓库（Remote Repository）**：托管在 GitHub / Gitee 等云端的代码仓库。
  - 使用 `git push` 把本地仓库的提交推送到远程仓库。
  - 使用 `git pull` 把远程仓库的更新拉取合并到本地仓库。

---

## 2. 常见命令

### 项目初始化与提交流程
```bash
# 初始化本地仓库，生成隐藏的 .git 目录
git init 

# 查看文件状态（红字表示未暂存/未跟踪，绿字表示已进入暂存区）
git status 

# 查看工作区与暂存区的具体代码改动
git diff 

# 把工作区的改动添加到暂存区
git add . 

# 把暂存区的改动提交到本地仓库（-m 后的说明应简洁概括本次提交）
git commit -m "<提交信息>" 

# 查看提交历史版本日志
git log 

# 丢弃工作区的未暂存改动（用暂存区的版本覆盖工作区）
git restore <文件名> 

# 将已暂存的文件撤回工作区（取消暂存）
git restore --staged <文件名> 
```

---

## 3. 连接远程仓库

### 创建远程仓库步骤
1. 登录代码托管平台（如 GitHub / Gitee），点击右上角 **【+】➔【New repository】**；
2. 填写仓库名（如 `git-learn`）；
3. **⚠️ 最关键的一步**：千万不要勾选“初始化仓库”、“添加 README”或“.gitignore”等选项，保持为**完全空白的仓库（Empty Repository）**；
4. 点击 **【Create repository】**，复制生成的仓库 HTTPS 地址。

### 本地与远程关联命令
```bash
# 1. 关联远程仓库，并给远程起标准别名为 origin
git remote add origin <远程仓库地址>

# 2. 将当前本地主分支重命名规范化为 main
git branch -M main

# 3. 首次推送到远程仓库（-u 建立上下游关联，后续只需 git push）
git push -u origin main

# （选学）修改远程仓库别名（如将旧别名改为新别名）
git remote rename <旧别名> <新别名>
```
> *注：第一次推送到远程仓库通常需要浏览器授权或配置 Token 认证。*

---

## 4. 日常开发标准流程

### 标准循环流
1. **修改代码**：在编辑器中编写业务逻辑；
2. **查看状态**：
   ```bash
   git status
   ```
   - 红色 `Untracked files`：全新未跟踪的文件；
   - 红色 `Changes not staged for commit`：已有文件发生了修改但未暂存；
   - 绿色 `Changes to be committed`：已暂存，等待提交。
3. **核对修改细节**：
   ```bash
   git diff
   ```
   - 绿色 `+`：新增的代码行；
   - 红色 `-`：删减的代码行。
4. **加入暂存区**：
   ```bash
   git add .
   ```
5. **提交快照**：
   ```bash
   git commit -m "<简洁明了的提交信息>"
   ```
6. **推送到远程**：
   ```bash
   git push
   ```
   *(因为首次推送时加了 `-u origin main`，日常开发只需输入 `git push`)*

---

## 小结：生命周期速查

```text
git init                  ➔  开辟本地仓库
git add .                 ➔  选入暂存区
git commit -m "..."       ➔  存档版本快照
git remote add origin ... ➔  牵线搭桥远程
git push -u origin main   ➔  首次同步上云
git push                  ➔  日常一键推送
```

## 5. 分支管理
### 分支类型
主分支： main 分支跑的是线上正式运行的业务，一旦写错，系统直接崩溃。
（真实的工作模式永远是：
从 main 切出一个属于你自己的分支（例如 feature-login）；
你在自己的分支里随便折腾、随便提交；开发完毕、测试通过后，再把它**合并（Merge）**回主干）
开发分支：dev / develop：日常开发分支，集成所有人的最新代码。
feature/*（功能分支）：比如 feature-login，开发完特定功能合并后即销毁。
hotfix/*（紧急修复分支）：线上突发重大 Bug 时，紧急切出来修补的分支。
### 分支维护
Fast-forward（快进合并）
场景：主分支在你离开后没有任何新提交，合并时 Git 只需要把主分支指针直接“往前推”，不产生额外的合并记录，最丝滑。
3-Way Merge（三方合并）
场景：主分支和你的功能分支各自都向前提交了新版本，Git 会自动把两边的改动融在一起，并生成一个新的“合并快照（Merge Commit）”。
Conflict（冲突）
场景：两个分支改了同一文件的同一行代码。此时 Git 不敢擅自决定，会停下来把两边的代码都标出来，让你手动决定“留谁的”。


### 常用命令
查看所有本地分支：git branch
创建并切换分支：git switch -c dev
在分支上提交新内容
切换到已有的分支：git switch <分支名>
切回主干：git switch main
合并分支：git merge dev
安全删除分支（功能合并后清理）：git branch -d <分支名>