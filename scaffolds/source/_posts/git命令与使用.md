---
title: git命令与使用（团队workfolw的分布使用）
date: 2017-7-16 10：53
tags:  git命令与使用
reward: true
---

# git命令与使用（添加.提交.暂存储藏.删除.推送.拉分支.标签.合并.处理冲突.reset.查看日志.等常用命令）

### 1.git 添加命令（提交的文件信息进入索引库）

    git add . 将修改的工作文件全部提交到暂存区
    git add -u 把当前目录下的traceked文件修改信息添加到索引库
    git add -i 命令查看<path>中被所有修改过或已删除文件但没有提交的文件，
    并通过其revert子命令可以查看<path>中所有untracted的文件，同时进入一个子命令系统。
    git add -h  查看git add 帮助文档
<!--more-->

### git 提交命令 
    git commit -m "fiirst  commit " 提交暂存的文件到本地仓库
    git commit 会提交所有文件但是也会在git bash 输入提交信息
    git commit --amend  修改上一次提交内容或提交说明

### git 暂存储藏命令（stash）
    git stash 将自己的工作仓储起来
    git stash list 查看自己的stash（你可能有多个）
    git stash show 查看自己未提交的修改有哪些
    git stash apply stash@<0>等 你可以恢复你想恢复的stash 版本
    git stash clear 清楚git栈stash记录
<font color=blue>使用git的时候，我们往往使用branch解决任务切换问题，
例如，我们往往会建一个自己的分支去修改和调试代码, 如果别人或者自己发现原有的分支上有个不得不修改的bug，我们往往会把完成一半的代码 commit提交到本地仓库，然后切换分支去修改bug，改好之后再切换回来。这样的话往往log上会有大量不必要的记录。其实如果我们不想提交完成一半或者不完善的代码，但是却不得不去修改一个紧急Bug，那么使用'git stash'就可以将你当前未提交到本地（和服务器）的代码推入到Git的栈中，这时候你的工作区间和上一次提交的内容是完全一样的，所以你可以放心的修 Bug，等到修完Bug，提交到服务器上后，再使用'git stash apply'将以前一半的工作应用回来。也许有的人会说，那我可不可以多次将未提交的代码压入到栈中？答案是可以的。当你多次使用'git stash'命令后，你的栈里将充满了未提交的代码，这时候你会对将哪个版本应用回来有些困惑，'git stash list'命令可以将当前的Git栈信息打印出来，你只需要将找到对应的版本号，例如使用'git stash apply stash@{1}'就可以将你指定版本号为stash@{1}的工作取出来，当你将所有的栈都应用回来的时候，可以使用'git stash clear'来将栈清空。</font>

### git 删除命令
    
     git rm -f 文件名称  删除commit过的文件
     git rm -r 文件或者文件夹 删除commit过的文件或者文件夹(本地仓库)
     git rm -h 删除帮助文档
     git rm -r -n --cached "bin/" //-n 执行命令时，是不会删除任何文件，而是展示此命令要删除的文件列表预览。（本地仓库）
     -n, --dry-run 演习（不删除列出列表）
     -q, --quiet 不列出删除的文件
     --cached 只从索引区删除
     -f, --force 忽略文件更新状态检查
     -r 允许递归删除
### git 推送将本地分支推送至远程主机
     git push origin master 将本地master分支推送至origin主机的master分支如果master 不在则会被重建
     git push origin :master 表示删除origin主机的master 分支
     git push origin --tags 推送tag分支
     git push origin 分支名称 推送你的本地分支到远程
     git push origin :分支名称  删除远程分支 
### git 拉分支的命令

     git branch  -a 查看远程分支
     git branch 分支名称 创建一个分支
     git checkout 分支名称  切换一个分支名称
     git fetch origin branchname:branchname 
     远程某各分支拉去到本地的branchname下，如果没有branchname，则会在本地新建branchname 
     git checkout -b 分支名称 创建分支并且切换到分支名称
     git push  origin 本地分支名称：远程分支名称 如果远程分支不在则在远程创建新的分支（中间没有空格）

### git 标签
     
     git tag#列出现有标签	
     git tag v0.1#新建标签
     git tag -a v0.1 -m &#039;my version 1.4&#039;#新建带注释标签
     git checkout tagname#切换到标签
     git push origin v1.5#推送分支到源上
     git push origin --tags#一次性推送所有分支
     git tag -d v0.1#删除标签
     git push origin :refs/tags/v0.1#删除远程标签

### git合并与解决冲突

    如果团队其他成员提交到了远程.首先你要先把你的修改文件提交到本地仓库
    git commit  -m "说明 "
    git pull 更新新的提交
    git status  查看冲突文件
    看下有多少文件冲突。
     对每个冲突文件：
     在编辑器中打开文件（比如 IntelliJ 或 Vim ）
     看看每个被冲突标记（“>>>>”和“<<<<”）围绕的区块。
     看看（被冲突标记的区块）是否有意义，每个作者的意图是什么，如果能弄清楚就解决掉。
     如果冲突标记无法理解，通常是这些文件改动很大，运行下面的命令：
     Shell
     git diff HEAD...the/other/branch -- path/to/conflicting/file
     git diff the/other/branch...HEAD -- path/to/conflicting/file
     这样做是为了看哪边改动较小
     修改冲突完成后(可以用合并工具一类的.在网上找个就行)
     git  add file 将文件添加索引
     git commit  -m "说明 " 
     git  push  origin master 提交到主干或者git push origin 本地分支名称   提交到分支

     如果是合并分支
     1. git  checkout  master 切换到主干
     2. git status 查看状态
     3. gitgit merge --no-ff branch（分支名称） 合并分支 --no-ff 会产生一个新的提交节点保证版本清晰
     4. git status 查看冲突文件
     5. git diff  文件名称  （冲突发生在哪里）
     6. 打开文件修改冲突
     7. git add 文件  将文件提价到索引
     8. git commit -m “提交说明” 
     9. git push origin  master 推送到远程分支
      
### git reset 撤销命令

    git reset –mixed：此为默认方式，不带任何参数的git reset，即时这种方式，它回退到某个版本，只保留源码，回退commit和index信息
    git reset –soft：回退到某个版本，只回退了commit的信息，不会恢复到index file一级。如果还要提交，直接commit即可
    git reset –hard：彻底回退到某个版本，本地的源码也会变为上一个版本的内容
    #回退所有内容到上一个版本 
    git reset HEAD^ 
    #回退a.py这个文件的版本到上一个版本 
    git reset HEAD^ a.py 
    #向前回退到第3个版本 
    git reset –soft HEAD~3 
    #将本地的状态回退到和远程的一样 
    git reset –hard origin/master 
    #回退到某个版本 
     git reset 057d 
    #回退到上一次提交的状态，按照某一次的commit完全反向的进行一次commit 
    git revert HEAD 

### 查看日志
     
    git  reflog 可以查看所有分支的所有操作记录（包括（包括commit和reset的操作），包括已经被删除的commit记录
    (即使你reset错误使用这个命令可以回退版本)
    git log则不能察看已经删除了的commit记录 
    git log --pretty=oneline 这个我比较常用的查看日志命令 
    
### 生成一个可供发布的压缩包
     git archive
	

###  最后上个图
![](http://jbcdn2.b0.upaiyun.com/2015/12/e57cf4e67ff5a3cd2a069981e320006f.png)

Workspace：工作区
Index / Stage：暂存区
Repository：仓库区（或本地仓库）
Remote：远程仓库

 下次写下  git工作流的使用！！！  感谢大家指出错误！！可以发我邮箱




