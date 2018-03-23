---
title: git rebase与merge的区别（推荐merge）
date:
tags:  git命令与使用
reward: true
---


## **git rebase与merge的区别**


# 前言

git merge与rebase使用与区别

<!--more-->

## git rebase 使用

git rebase 用于把一个分支的修改合并到当前分支 。假设你现在有一个远程分支.创建mywork分支.

      git checkout -b mywork origin 
假设远程分支已经有两个提交.如图。
![](C://hexo_blog/themes/spfk/source/img/bj.jpg)

现在我们在这个分支做一些修改.然后生成两个提交（commit）.
#### 命令
      touch a.txt
      touch b.txt
      git add -A
      git commit -m "commt"
      git push origin master


但是与此同时别人也在origin上面做了一些修改并且提交了.这意味着 origin与mywork各自前进了.它们之间关系图如下：
![](http://upload-images.jianshu.io/upload_images/697285-608d1da96f1ea926.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


在这里 .你可以用git pull 命令把分支上的修改合并.结果看起来就像是一个新的合并的提交（merge commit）

在这里形成了一个新的提交 C7 .但是.如果你想让mywork分支历史看起来更加整洁像没有合并过一样.你可以使用git rebase

![](http://upload-images.jianshu.io/upload_images/697285-b52fdd23cfc86d35.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 命令
      git checkout mywork
      git rebase origin 
这些命令会把你的"mywork"分支里的每个提交(commit)取消掉，并且把它们临时 保存为补丁(patch)(这些补丁放到".git/rebase"目录中),然后把"mywork"分支更新 为最新的"origin"分支，最后把保存的这些补丁应用到"mywork"分支上。

![](http://upload-images.jianshu.io/upload_images/697285-d12ab4a3d26ebcfd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当'mywork'分支更新之后，它会指向这些新创建的提交(commit),而那些老的提交会被丢弃。 如果运行垃圾收集命令(pruning garbage collection), 这些被丢弃的提交就会删除. （请查看 git gc)
![](http://upload-images.jianshu.io/upload_images/697285-b48791d46af20e9f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 解决冲突

在rebase的过程中，也许会出现冲突(conflict). 在这种情况，Git会停止rebase并会让你去解决 冲突；在解决完冲突后，用"git-add"命令去更新这些内容的索引(index), 然后，你无需执行 git-commit,只要执行:
#### git 命令

      git rebase --continue
      git rebase --abort
这样git会继续应用(apply)余下的补丁。
在任何时候，你可以用--abort参数来终止rebase的行动，并且"mywork" 分支会回到rebase开始前的状态。

## gitrebase与merge区别

查看历史


![](http://upload-images.jianshu.io/upload_images/697285-9ae8ecdf2a9f7b5b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




当我们使用Git log来参看commit时，其commit的顺序也有所不同。

假设C3提交于9:00AM,C5提交于10:00AM,C4提交于11:00AM，C6提交于12:00AM,

对于使用git merge来合并所看到的commit的顺序（从新到旧）是：C7 ,C6,C4,C5,C3,C2,C1

对于使用git rebase来合并所看到的commit的顺序（从新到旧）是：C7 ,C6‘,C5',C4,C3,C2,C1

因为C6'提交只是C6提交的克隆，C5'提交只是C5提交的克隆，
从用户的角度看使用git rebase来合并后所看到的commit的顺序（从新到旧）是：C7 ,C6,C5,C4,C3,C2,C1

另外，我们在使用git pull命令的时候，可以使用--rebase参数，即git pull --rebase,这里表示把你的本地当前分支里的每个提交(commit)取消掉，并且把它们临时 保存为补丁(patch)(这些补丁放到".git/rebase"目录中),然后把本地当前分支更新 为最新的"origin"分支，最后把保存的这些补丁应用到本地当前分支上。

使用方式：
$ git pull --rebase <远程主机名> <远程分支名>:<本地分支名>

作者：烟雨随风
链接：http://www.jianshu.com/p/cca69cb695a6（转载）


	