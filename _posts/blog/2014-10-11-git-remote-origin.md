---
layout:	post
title: 	远程服务器端初始化git仓库
category: blog
description: 发布项目时，首先就需要将本地代码上传到服务器，这里将介绍如何使用git管理代码。
---

## 远程服务器初始化git项目

很多时候程序员需要将代码`push`到一个远程仓库，而这个仓库很多时候没有`GitHub`那么来得便捷，比如在产品发布时候，需要在服务器端来获取本地代码并使用`git`来管理，所以学会在终端使用命令行创建`git`仓库是很有必要的哦！

### 首先需要在远程服务器上初始化空的仓库

```
$ mkdir /repository/project  #在仓库目录下创建项目文件夹
$ cd /repository/project	#转到此文件夹
$ git –bare init  #初始化此文件夹为一个空的仓库
```

### 在本地的git仓库添加一个远程仓库

```
$ cd  ~/project
$ git remote add origin ssh://myserver.com/repository/project
```

这时候，本地的 `.git/config` 就增加了新仓库的远程连接

### 将本地的 master 分支 push 到远程的分支

```
$ git push origin master
```

这样，本地代码就被推送到远程服务端了，并且是由`git`做版本控制，这其中的好处你懂的！

** 关于初始化仓库时使用`git –bare init`，而不是`git init`的补充说明 **

> 如果使用了`git init`初始化，则远程仓库的目录下，也包含`work tree`，当本地仓库向远程仓库`push`时，如果远程仓库正在`push`的分支上（如果当时不在`push`的分支，就没有问题），那么`push`后的结果不会反应在`work tree`上，也即在远程仓库的目录下对应的文件还是之前的内容，必须得使用`git reset --hard`才能看到`push`后的内容。



