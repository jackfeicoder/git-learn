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
git add .        # 添加所有文件
git add <文件名> # 添加指定文件

# 撤销暂存区文件（如果加错了想撤销）：
git restore --staged <文件名>
git restore --staged .        # 撤销所有暂存

# 提交到本地仓库（-m 后的说明应简洁概括本次提交）
git commit -m "<提交信息>" 

# 提交信息打错字了：修补最后一次提交
git commit --amend -m "写对的新提交信息"

# 撤销已有的 commit（仅撤销提交，保留代码在暂存区，尚未推送到远程时适用）
git reset --soft HEAD~1       # HEAD~1 表示倒退一个版本，--soft 极度温柔

# 查看提交历史版本日志
git log 

# 丢弃工作区的未暂存改动（用暂存区的版本覆盖工作区）
git restore <文件名> 
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
   # 偷懒技巧：对已有文件合并执行 add 和 commit
   git commit -am "<提交信息>"  # -a (all) + -m (message)，但不包含未跟踪的新文件
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

---

## 5. 分支管理

### 分支类型与应用场景
- **主分支（`main` / `master`）**：运行线上正式业务的代码，稳定至关重要。
  - **完整大型项目规范（Git Flow）**：`main` 为上线稳定分支；`dev` 为日常开发集成总分支；实现具体功能时从 `dev` 切出功能分支，测试通过后再合回 `dev`；阶段性版本通过后统一把 `dev` 合入 `main` 上线。
  - **个人 / 开源项目（GitHub Flow）**：直接从 `main` 切出 `feature` 功能分支，提 Pull Request（代码审查），审查通过后直接合进 `main` 自动触发部署。
- **开发分支（`dev` / `develop`）**：日常开发分支，集成所有人的最新代码。
- **功能分支（`feature/*`）**：如 `feature-login`，开发完特定功能合并后即销毁。
- **紧急修复分支（`hotfix/*`）**：线上突发重大 Bug 时，紧急切出来修补的分支。

### 分支合并模式与术语
- **Fast-forward（快进合并）**：主分支在你离开后没有任何新提交，合并时指针直接往前推，不产生多余的合并节点。
- **3-Way Merge（三方合并）**：主分支和功能分支各自都有新提交，Git 自动融合成一个全新的“合并快照（Merge Commit）”。
- **Conflict（冲突）**：两个分支改了**同一文件的同一行代码**，Git 停下来标出差异，由开发者人工决策。

### 常用分支命令
```bash
# 查看所有本地分支（当前分支带 * 号）
git branch

# 创建并立即切换分支
git switch -c dev

# 切换到已有的分支
git switch <分支名>

# 安全删除分支（功能合并后清理）
git branch -d <分支名>

# 强制删除未合并的分支
git branch -D <分支名>
```

#### 合并原则：想把代码合给谁，就先切换（switch）到谁那里
```bash
# 例 1：把功能合入开发分支
git switch dev            # 1. 站到接收者 dev 分支上
git merge feature-login   # 2. 把功能分支吸纳进来

# 例 2：发版上线
git switch main           # 1. 站到正式版 main 分支上
git merge dev             # 2. 把测试通过的 dev 吸纳进来
```

---

### 合并冲突解决

#### 场景 1：两个分支修改了同一行代码产生内容冲突
**示例过程**：
1. 在 `main` 分支上修改 `hello.txt` 并提交：
   ```bash
   git commit -am "main添加内容"
   ```
2. 切换到功能分支也修改 `hello.txt` 并提交：
   ```bash
   git switch feature-user
   git commit -am "功能分支修改内容"
   ```
3. 切回主分支执行合并：
   ```bash
   git switch main
   git merge feature-user
   ```
4. 此时终端会报出冲突：
   ```text
   CONFLICT (content): Merge conflict in hello.txt
   Automatic merge failed; fix conflicts and then commit the result.
   ```
5. 打开文件会看到 Git 的冲突标记：
   ```text
   <<<<<<< HEAD
   main: 你好！这是【main分支】写的内容。
   =======
   feature-user:功能分支修改了 hello.txt 文件内容
   >>>>>>> feature-user
   ```
   - `<<<<<<< HEAD`：当前所在分支（`main`）的代码起点；
   - `=======`：楚河汉界（分界线）；
   - `>>>>>>> feature-user`：传入分支（`feature-user`）的代码终点。

**解决方式**：
- **招式一（VS Code 一键裁决）**：点击代码上方的浮动按钮（`采用当前更改` / `采用传入的更改` / `保留双方更改` / `比较变更`）。
- **招式二（纯手工删改）**：把 `<<<<<<<`、`=======`、`>>>>>>>` 符号当普通文本直接删掉，保留正确的最终代码。
- **裁决完毕后的标准提交流程**：
  ```bash
  # 1. 标记冲突已解决
  git add .

  # 2. 正式完成合并提交
  git commit -m "fix: 解决 hello.txt 合并冲突"
  ```
- **半途后悔救命命令**：
  ```bash
  # 一键撤销正在进行的合并，恢复到合并前
  git merge --abort
  ```

---

#### 场景 2：合并完成后发现完全错了，如何恢复历史版本？

##### 情况 A：尚未推送到远程（纯本地后悔）
```bash
# 最推荐：利用 Git 记忆点一键秒回合并前
git reset --hard ORIG_HEAD

# 或者倒退一步
git reset --hard HEAD~1
```
*回退后即可重新在本地排查代码、重新合并并提交推送。*

##### 情况 B：已经推送到远程（公共分支安全回滚，不用 -f）
**核心心智模型**：
```text
历史长河：
A(合并前) ────► B (合并错了且已推送) ────► C (revert撤销提交，内容等同于A) ────► D (改对后的新合并)
```

**操作步骤**：
```bash
# 1. 生成反向抵消提交 C（内容与 A 100% 一模一样，-m 1 表示保留主分支基准）
git revert -m 1 <B的Commit_ID>

# 2. 正常推送（完全不需要 -f，团队协作零风险！）
git push
```
*远程代码恢复为 A 的状态后，在本地重新改对代码并合并，再正常 `git push` 生成正确的版本 D。*

> 💡 **实战案例示例：如果执行 `git revert` 发生冲突时的标记样式**
> 当撤销提交发生冲突时，下方的标记会显示 `parent of <Commit_ID>`：
> ```text
> <<<<<<< HEAD
> git push
> =======
> >>>>>>> parent of fe1803d (冲突和回滚问题)
> ```
> *（注：这代表 Git 在尝试倒退 `fe1803d` 时，与当前 HEAD 产生重叠冲突）*


##### 情况 C：（补充）个人私有分支暴力覆盖流（使用 -f）
```bash
# 1. 本地硬回滚到旧版本 A
git reset --hard <A的Commit_ID>

# 2. 强制覆盖远程（⚠️ 仅限个人分支使用，会彻底抹去 B 的历史）
git push -f origin main
```

## 6. 代码审查与多人协作：Pull Request (PR)

Pull Request（简称 PR）是团队多人协作的核心机制：开发者将自己的独立分支推送到远程后，向主分支（`main` / `dev`）发起“合并申请”，由项目负责人或团队成员进行代码审查（Code Review），确认无误后再合并入主干。

---

### 第一幕：邀请协作者（团队授权）

在真实团队中，新人入职第一步是被管理员拉入项目仓库：
1. **大号（项目所有者）登录 GitHub**，进入目标仓库：[https://github.com/jackfeicoder/git-learn](https://github.com/jackfeicoder/git-learn)；
2. 点击仓库右上角的 **【Settings】**（齿轮图标）；
3. 在左侧菜单栏选择 **【Collaborators】**（协作者）；
4. 点击绿色的 **【Add people】** 按钮；
5. 输入小号的 GitHub 用户名或注册邮箱，发送合作邀请。

---

### 第二幕：协作者（小号）接受邀请与本地开发

1. **接受邀请**：用小号登录 GitHub，在通知小铃铛或注册邮箱中确认并点击 **【Accept invitation】**（正式入职）。
2. **克隆项目（无需 git init）**：
   ```bash
   # 进入工作目录，直接全量克隆（自动配置好 .git 和远程 origin）
   git clone https://github.com/jackfeicoder/git-learn.git
   cd git-learn
   ```
3. **配置小号开发者身份（记录作者信息）**：
   ```bash
   git config user.name "你的小号GitHub名字"
   git config user.email "你的小号注册邮箱"
   ```
4. **遵守规范：切出新功能分支开工**：
   ```bash
   git switch -c feature-order
   ```
5. **编写业务代码并提交**：
   ```bash
   echo "小号开发：新增了商城订单结算模块！" > order.txt

   git add order.txt
   git commit -m "feat: 小号完成了订单结算模块功能"
   ```
6. **推送到远程仓库**：
   ```bash
   git push -u origin feature-order
   ```
   - `-u`（`--set-upstream`）：建立本地分支与远程同名分支的追踪绑定，后续只需简写 `git push`；
   - `origin`：远程仓库的标准别名；
   - `feature-order`：在 GitHub 远端同步创建并推送此分支。

---

### 第三幕：发起 Pull Request (小号)

1. 推送后，打开 GitHub 仓库主页，页面顶部会自动弹出黄色提示条：
   `feature-order had recent pushes ...`，点击右侧绿色的 **【Compare & pull request】** 按钮；
2. **核对分支流向**：`base: main ◄── compare: feature-order`（请求将 `feature-order` 合入 `main`）；
3. 确认绿色提示：`Able to merge`（无冲突，可自动合并）；
4. 填写本次 PR 的标题和功能说明（如：`feat: 完成了商城订单结算模块`）；
5. 点击 **【Create pull request】**，正式提交审查请求。

---

### 第四幕：代码审查与关键按钮功能（大号技术负责人）

创建 PR 后，负责人进入 PR 详情页进行评审：
1. 点击顶部的 **【Files changed】** 标签页（网页版 `git diff`），可逐行查看新增或修改的代码；
2. 鼠标悬停在代码行左侧的蓝色小加号 `+`，即可留评语。

#### 💡 评审常用按钮功能解析：
- **`Start a review`（开始评审 / 草稿批注）**：
  开启草稿模式。所写评语为 `Pending` 状态，不会即时骚扰对方。全部文件检查完毕后，点击右上角 **【Finish your review】** 统一打包发出，可选择 `Approve`（批准通过）或 `Request changes`（要求整改）。
- **`Reply`（单条即时回复）**：
  不走草稿流程，发送后立即公开并即时通知对方，适合快速问答。
- **`Resolve comment`（标记问题已解决）**：
  日常高频！当作者修改完毕或讨论一致后点击，该讨论串自动折叠，保持评审界面清爽。
- **`Add to batch`（加入批量建议）**：
  将多处代码修改建议打包，方便作者在网页端一键批量采纳提交。

> **职场口诀**：平时单条沟通点 `Reply`；全套正式审查点 `Start a review`；改完通过点 `Resolve comment`。

---

### 第五幕：正式合并与后续同步（打扫战场）

1. **执行合并**：在 PR 的 **【Conversation】** 页面底部，点击绿色大按钮 **【Merge pull request】** ➔ **【Confirm merge】** 完成线上合入。
2. **合并后的团队代码同步规范**：
   - **参与本次合并的人（小号）必须【立刻】同步**（因为合并发生在云端，本地 `main` 仍落后）：
     ```bash
     git switch main
     git pull
     git branch -d feature-order  # 删除已合并上线的本地功能分支，保持整洁
     ```
   - **其他未参与的同事【按节奏】同步**：
     - **每天早晨上班第一件事（行业铁律）**：
       ```bash
       git switch main   # 或切换到 dev
       git pull
       ```
     - **开辟新功能分支之前必须先 pull**：确保站在最新的代码基底上开发，从源头避免后续代码冲突！



## 7. .gitignore 忽略文件机制

在执行 `git add .` 时，Git 会默认暂存文件夹里的一切变动。但为了项目的安全与整洁，有些文件是**绝对不能、也不应该提交到版本库**的。

---

### 一、 核心作用：为什么要使用 .gitignore？

1. **敏感密码与密钥（防泄密、防被盗刷）**：
   - 包含数据库密码、Token 密钥的文件（例如 `.env`、`config.secret.json`、私钥证书）。
   - *(每年都有新手不小心把云服务器密码、微信支付密钥 push 到公开 GitHub，导致服务器被黑客黑掉挖矿！)*
2. **体积巨大的第三方依赖包（防仓库臃肿）**：
   - 例如前端的 `node_modules/`（动辄几百兆）、Python 的虚拟环境 `venv/`、Java 的 `target/` 编译产物。
   - 依赖包应由团队成员本地各自安装，上传到 Git 会让仓库庞大无比、推送和拉取极其缓慢。
3. **个人开发环境配置与系统垃圾（防打架冲突）**：
   - 编辑器自动生成的配置文件夹：`.idea/`（IntelliJ）、`.vscode/`。
   - 操作系统生成的隐藏垃圾：Windows 的 `Thumbs.db`、Mac 的 `.DS_Store`、运行日志 `*.log`。

---

### 二、 常见语法规则与模板

在项目根目录下创建一个名为 **`.gitignore`** 的纯文本文件（注意前面有一个英文点 `.`），常见书写规则如下：

```gitignore
# 1. 忽略指定名称的文件
secret.txt
.env

# 2. 忽略某种后缀的所有文件（通配符 *）
*.log
*.tmp

# 3. 忽略整个目录及其内部所有内容（末尾加 /）
node_modules/
dist/
venv/
.idea/

# 4. 仅忽略根目录下的文件，不忽略子目录里的（开头加 /）
/todo.txt

# 5. 例外规则（加 ! 表示取反，特例保留）
!important.log     # 即使上面写了 *.log，依然不忽略这个 important.log
```

---

### 三、 ⚠️ 面试与日常最常踩的大坑（已追踪文件失效问题）

> **“我已经把 `secret.txt` 写进 `.gitignore` 了，为什么 `git status` 还是能看到它，或者它还是被提交了？”**

- **原因**：
  `.gitignore` **只能忽略从未被 Git 跟踪过的全新文件（Untracked）**！如果这个文件在写进 `.gitignore` 之前，你曾经对它执行过 `git add` 或已经 `commit` 过了，Git 已经建立了它的档案，此时 `.gitignore` 对它就会**完全失效**！
- **救命解法（从暂存区踢出，但保留在本地电脑）**：
  ```bash
  # 1. 从 Git 的版本监控中剔除该文件（磁盘上的物理文件不会被删除）
  git rm --cached secret.txt

  # 如果是整个文件夹被误跟踪了：
  git rm -r --cached node_modules/

  # 2. 提交一次这次剔除操作，让 .gitignore 重新生效：
  git commit -m "chore: 将已跟踪的文件从版本库移除并由 .gitignore 托管"
  ```



## 8. git stash 临时储藏现场（紧急任务切换神器）

### 典型痛点场景
你在 `feature-order` 分支写订单功能写到一半，代码全是红线报错，根本没法 commit。此时测试或老板突然冲过来说：“线上主分支有个致命 Bug，立刻停下手头的事去修一下！”
- 如果你直接 `git switch main` 切换分支，Git 会直接报错拒绝（提示工作区有未提交的改动）；
- 如果你为了切分支而随便 commit 一下，又会污染版本历史，留下类似“临时保存”的垃圾提交。

---

### 标准解决流程（4 步极简流）

```bash
# 1. 瞬间把写了一半的代码“塞进私密口袋”，工作区秒变干净
git stash

# 2. 潇洒切去主分支修 Bug、提交、推上线
git switch main
# ...（修Bug，commit，push）...

# 3. 修完切回你的功能分支
git switch feature-order

# 4. 从口袋把半成品掏出来，继续刚才的思路写代码！
git stash pop
```

> **💡 常用辅助小命令**：
> - `git stash list`：查看“口袋”里存了多少次临时工作现场；
> - `git stash pop`：恢复最近一次的储藏，并把口袋里的记录清空；
> - `git stash apply`：恢复最近一次的储藏，但口袋里依然保留备份。

---

## 9. 其他进阶实用技能

### 9.1 git tag（版本发布里程碑标签）
- **作用**：当项目阶段性开发完成、正式发版上线时，给该提交打一个“永久版本号”（如 `v1.0.0`）。在 GitHub 上会自动生成一个正式的 Release 压缩包下载页。
- **用法**：
  ```bash
  # 1. 本地打附注标签（-a 指定标签名，-m 填写发布说明）
  git tag -a v1.0.0 -m "Release version 1.0.0 正式上线"

  # 2. 将标签推送到 GitHub
  git push origin v1.0.0

  # （一次性推送本地所有未推送的标签）
  git push origin --tags
  ```

---

### 9.2 git cherry-pick（精准“摘樱桃”）
- **作用**：同事在另一个分支提交了 10 个功能，你只需要其中的**某一个特定 commit**（比如他写的一个公共工具函数或修的一个公共 Bug），你不想把他的整个分支都合并过来。
- **用法**：
  ```bash
  # 精准把指定的某一次提交“摘”到你当前的分支上
  git cherry-pick <该提交的Commit_ID>
  ```