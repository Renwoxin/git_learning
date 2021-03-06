# Git学习笔记
![20170703094821967](img/20170703094821967.jpeg)

## 创建ssh key
```bash
git config --global user.name "xxy"
git config --global user.email 775415344@qq.com

ssh-keygen -t rsa -C "775415344@qq.com"
# 三次回车

# 查看秘钥
cd ~/.ssh
vim id_rsa.pub
# 复制秘钥
# 打开Github网站进行登录
# 到个人设置页Personal Settings
# 找到SSH and GPG keys
# 选择新建SSH key：new ssh key
# 填写和粘贴公钥内ring（不含中文）
```

# 1. 创建git项目
## 克隆远程版本库
```bash
git clone git@github.com:aixingxy/gitlearning.git  # 会自动创建master分支

# 如果想在克隆远程仓库的时候，自定义本地仓库的名字，可以使用如下命令
git clone git@github.com:atommutou/gitlearning.git my_repp
```

## 创建本地版本库
## 添加远程库
```bash
mkdir gitlearning
cd gitlearning
echo "# gitlearning" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:atommutou/gitlearning.git
git push -u origin master
# 第一次推送master分支，加上-u参数，Git不但会把本地的master分支内容推送的远程新的master，还把本地master分支和远程的master分支关联起来。
# 以后推送，使用下面的语句就可以了
git push origin master
```
## 更新提交到仓库
工作目录下的每一个文件都不外乎两种状态：`已跟踪`和`未跟踪`。
- 已跟踪是指那些被纳入了版本控制的文件，在上一次快照中有它们的记录没在工作一段时间后，它们的状态可能处于未修改，已修改或已放入暂存区。
- 工作目录中除了已跟踪文件以外的所有其他文件都属于未跟踪文件，它们既不存在于上次快照的记录中，也没有放入暂存区。
初次clone某个仓库的时候，工作目录中的所有文件都属于已跟踪文件，并处于未修改状态。

### git add的功能
```bash
git add file  # 添加制定文件
git add .  # 添加工程下的所有文件
```
- 可以用来开始跟踪新文件
- 把已跟踪的文件放到暂存区
- 合并是把有冲突的文件标记为已解决的状态



# 2. 分支操作
## 创建分支
```bash
git checkout -b dev  # 创建分支并直接切换
# 相当于下面两个句子
git branch dev  # 创建分支
git checkout dev  # 切换分支
```

## 查看分支
```bash
git branch  # 当前分支钱前面会有一个*号
```

## 切换分支
```bash
git checkout dev
```

## 合并分支
```bash
git merge dev  # 用于合并指定分支到当前分支
# 使用Fast-forward
```
## 查看分支合并情况
```bash
git log --graph --pretty=oneline --abbrev-commit
```
## 切换远程分支
```bash
git branch -a # 列出所有分支名称
# remotes/origin/dev
# remotes/origin/release
git checkout -b dev origin/dev  # checkout远程的dev分支，在本地起名为dev分支，并切换到本地的dev分支
git checkout -b release origin/release
git checkout dev  # 直接切换远程分支
```

## 删除分支
```bash
git branch -d dev
```

# 3. 后悔操作
## 版本回退
在Git中，用HEAD表示当前版本，上一个版本就是HEAD^，上上个版本就是HEAD^^，上100个版本HEAD~100

```bash
# 使用--hard强制恢复到某次提交，并且git log中也不会显示上一次的提交
git reset --hard HEAD^
```
如果希望再次回到最新提交，可以通过git reflog查看每一条记录，来寻找commit_id,再使用
```bash
git reset --hard commit_id
```
## 丢弃工作区的修改
```bash
git checkout -- xx.file  #放弃单个文件
git checkout .  #放弃整个工程 
```

场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令```git checkout -- file```

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令```git reset HEAD file```，就回到了场景1，第二步按场景1操作。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库

## 撤销commit
场景：当你commit之后，发现自己忘记提交一些文件，又不想再创建一个commit，可以撤销上一次的提交``git commit --amend "new description"`，然后再add遗漏的文件到缓存区，再提交，确认提交之后，便可以将本地仓库的文件提交到远程仓库中，即`git push origin 仓库名`

## 显示尚未暂存的改动，而不是上次提交以来所做的所有改动。
```bash
git diff # 查看已经add的文件与工作区文件的不同
git diff --cached # 查看暂存区与提交的区别，也可以用git diff --staged
```

## 跳过使用暂存区
```bash
git commit -a "描述" #自动把所有已经跟踪的过的文件暂存起来并一并提交
```

## 比较远程库与本地的区别
```bash
git fetch origin
git diff master origin/master
```
## 移除文件
```bash
# 手动rm文件，文件还没有add到暂存区，git status会显示文件被删除，然后commit之后，这个文件就不会在纳入管理库了
rm file
# 文件已经add到暂存区，此时想要删除文件，使用下面的句子
git rm -f file # 注意这样文件也是被物理删除的
# 想把文件从git仓库中删除（从暂存区域移除），但仍然希望保留在当前工作目录中。
# 就是想保留文件在磁盘，但不想让git继续跟踪。
# 当你忘记添加.gitignore文件，不小心把一个很大的日志文件添加到暂存区，就可以使用这个方法
git rm --cached file # 不论文件是已经暂存还是已经提交，用这个方法都能删掉，且不会删掉物理文件
```
## 查看日志
```bash
git log
git log -p # 显示每次提交内容的差异
git log -2 # 显示最近两次提交
git log --stat # 显示每次提交的简短统计信息
```
## 查看日志详细信息
```bash
# 现根据git log查看提交ID，然后使用下面的句子，查看详细的提交信息
git show commit_id
```

## 查看远程仓库
```bash
git remote  # 列出每一个指定的远程仓库服务器
git remote -v  # 会显示远程仓库和URL
```
## 从远程仓库中抓取与拉取
这个命令会访问远程仓库，从中拉取所有还没有的数据。执行完成以后，将会拥有那个远程仓库中所有分支的引用，可以随时合并或查看
注意

`git fetch` 命令会将数据拉取到本地，它并不会合并会修改当前的工作。必须手动将其并入你的工作区。

`git pull` 命令会从最初克隆的服务器上抓取数据并走动尝试合并到当前所在的分支。

`git clone` 命令会自动设置本地master分支跟踪克隆的远程的master分支。

## 推送到远程仓库
```bash
# 将master分支推送到origin服务器上时（克隆时会自动帮你设置好两个名字），使用下面这个命令
git push origin master
```
当你和其他人在同一时间克隆，他们先推送到上游然后你再推送到上游，你的推送将毫无疑问地被拒绝。你必须先将他们的工作拉取下来并将其合并到你的工作后才能推送。

## 查看远程库
```bash
git remote show origin
```

## 远程仓库的移除与重命名
```bash
# 将gs重命名为newgs
git remote rename gs newgs # 注意这将修改你的远程分支的名字
# 将newgs删除
git remote rm newgs
```
## Git隐藏(stash)操作
- 你正在写代码，突然出现一个紧急bug要修复，你既不能提交代码，也不能丢弃当前的代码，你可以使用`git stash`将当前工作隐藏起来，存放到堆栈上。
- 通过`git stash list`命令查看已存在更改的列表。
- 通过`git stash pop`命令从堆栈中删除更改并将其放置在当前的工作目录中。

## 移动文件
```bash
git mv file dir/ # 将文件移动到dir文件下，之后再提交
```


## 冲突文件格式
```txt
<=====
//冲突，自己
======
//冲突，他人
====>
```


# 程序开发流程
1.比如说master分支是生产环境的代码，每次开发前我会先pull一下最新的master的代码，然后checkout出来一个新的分支，比如说dev分支`git checkout -b dev`

2.然后新代码都会写在dev分支上，开发测试完成后，add，commit。

3.然后checkout回master分支`git checkout master`，git pull最新的master代码。

4.与自己的dev分支进行diff操作看看，都有什么地方不同（主要是看，除了自己修改的，还有什么）
```bash
# linux的重定向命令
git diff [branchA] [branchB] > a.txt  # > 表示覆盖
git diff [branchA] [branchB] >> a.txt  # >> 表示追加

```
5.再`git merge dev`分支进行合并，在master分支下`git push origin master`

6.新手经常出现的版本冲突通常是大家同时修改了同一模块的东西。
