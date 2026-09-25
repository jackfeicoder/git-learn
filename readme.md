# git 基础
## 1.概念
**工作区**：写代码的地方
**暂存区**：保存改动的地方  git add .会把工作区的改动提交到暂存区
git开始监视这些文件的变动，使用git diff可以查看文件的改动

**本地仓库**：保存项目所有历史版本的地方  git commit会把暂存区的改动提交到本地仓库

**远程仓库**：存储在github等上的代码仓库，使用git push会把本地仓库的改动提交到远程仓库，使用git pull会把远程仓库的改动拉取到本地仓库


## 2.常见命令
- 初始化项目
    ```bash
    git init 
    # 之后生成一个.git文件，创建本地仓库
    # 之后使用git add . 把工作区的改动提交到暂存区
    # 之后使用git commit把暂存区的改动提交到本地仓库
    # git commit -m "<信息>" # -m 表示message，即提交信息。信息应该简洁的概括你本次提交的内容，
    # git status 查看文件状态 ，有未提交的改动会显示红色，没有会显示绿色
    # git diff 查看暂存区和工作区的文件改动
    # git log 查看提交历史，能看到一串由数字和字母组成的 Commit ID、作者信息、提交时间以及刚才写的信息
    # git restore <文件名> # 撤销工作区的改动，把文件恢复到暂存区
    
    ```
## 3.连接远程仓库
登录你的平台，点击右上角的【+】➔【新建仓库（New repository）】；
填写仓库名（比如起名叫 git-learn）；
⚠️ 最关键的一步： 千万不要勾选任何“初始化仓库”、“添加 README 文件”或“添加 .gitignore”的选项！ 保持它为一个完全空白的仓库（Empty Repository）。
点击【创建仓库】。
创建完成后，页面上会显示一个仓库地址（形如 https://.../git-learn.git）。

1. 关联远程仓库
告诉本地 Git：远程仓库的地址在哪里，并给它起个标准的别名叫 origin：

```bash
git remote add origin <远程仓库地址>
```
(GitHub 现在的默认主分支名称叫 main，而我们本地刚才初始化默认是 master，这条命令将分支名规范化为 main)

```bash
git branch -M main
```
- 远程仓库主分支重命名
    ```bash
    git remote rename <src_name> <des_name>
    ```

- 把本地仓库的内容推送到远程仓库

```bash
git push -u origin main
```
第一次推送到远程仓库需要输入用户名和密码或者浏览器认证

## 小结

完整生命周期：
```bash
git init（开辟仓库）
git add .（选入暂存区）
git commit -m "..."（存档快照）
git remote add origin <url>（牵线搭桥）
git push -u origin main（同步上云）
```


## 4.日常开发
1. 修改文件
2. 查看当前状态
git status
未在暂存区的是红色的 Untracked files（未跟踪），在的是 Changes not staged for commit: modified: readme.md。 因为 Git 已经在监控这个文件了，它知道你对它做了修改
3. 查看具体改动了什么
git diff
+ 号（通常显示为绿色）表示你新增的行；如果删除了内容，会用 - 号（通常显示为红色）标出。 提交前，用git diff看下
4. 提交改动
git add .
# 如果你已经创建了git仓库，没有执行git remote add origin <url>，则没有下一步
git commit -m "<信息>"
5. 同步到远程仓库
git push origin main

日常流程 
1.改代码
2.git status 查看哪些文件改动了（可以跳过）
3.git diff 查看具体改动了什么
4.git add . 把改动添加到暂存区
5.git commit -m "<信息>" 把暂存区的改动提交到本地仓库
6.git push origin main 把本地仓库的改动提交到远程仓库