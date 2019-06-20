---
title: git-commond
date: 2019-06-05 08:37:06
tags: git
---


> 记录一下git的命令


### 取消跟踪某个文件

```
git rm --cached file_name
git rm -r --cached file_name
```

### 添加忽略文件

```
vim .gitignore
```

忽略 idea 文件夹


*.idea 

### git branch

####  git branch

查看所有本地分支

```
$ git branch
  dev-branch
* feature-branch
```

#### git branch -r

远程分支

#### git branch -a

所有分支，包块本地  and 远程

```
$ git branch -a
  dev-branch
* feature-branch
  remotes/origin/HEAD -> origin/dev-branch
  remotes/origin/dev-branch
  remotes/origin/feature-branch
  remotes/origin/master
```

#### git branch dev 

创建  dev 分支

#### git branch -d/D dev 

删除  dev 分支 (-D 强制删除)

#### git branch -vv

查看本地分支对应的远程分支

#### git branch -m oldName newName

给分支重命名　　　　　　

### git merge

在当前分支：git merge 目标分支

将目标分支的 merge 到 当前分支

```
beer@DESKTOP-E819HOC MINGW32 /e/code/ipreon-api/datahub-api (feature-branch)
$ git branch
  dev-branch
* feature-branch

beer@DESKTOP-E819HOC MINGW32 /e/code/ipreon-api/datahub-api (feature-branch)
$ git merge dev-branch
Updating c8589fe..f4bd0a7
Fast-forward
 datahub-api/pom.xml                                |  24 +-
 .../ipreon/api/datahub/DataHubApiApplication.java  |   4 -
 .../ipreon/api/datahub/config/ExceptionAspect.java |  86 +++++
```

<!--more-->