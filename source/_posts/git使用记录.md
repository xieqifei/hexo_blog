---
title: git使用记录
top: false
cover: false
toc: true
mathjax: false
date: 2020-07-12 01:03:53
password:
summary: 这几天在做oneindex的修改版本，对git仓库有了更多的接触。之前使用git仓库都是用来做博客的静态资源托管。最近一段日子才正式使用其作为代码仓库。
tags: Git
categories: 其他
---



# 1：git指令

```shell
git clone https://github.com/user/repo/program.git
```

将远程仓库克隆到git打开的目录。

```shell
git switch -c dev
```

新建并切换至dev分支。

```shell
git switch dev
```

切换到dev分支

```shell
git add .
```

将所有变更提交至缓冲区

```shell
git commit -m "message"
```

为缓冲区的变更添加更新的提示

```shell
git push origin dev
```

将本地缓冲区的变更更新到远程仓库dev分支。

```shell
git merge dev
```

将dev分支的内容更新至当前所在分支。

```shell
git pull origin dev
```

拉去远程仓库dev分支的内容到本地仓库

# 2：.gitignore

在更新远程仓库时，可以指定部分本地仓库的文件/文件夹不推送至远程仓库。

在仓库根目录下新建`.gitignore`文件，直接编辑内容即可

```
/config/*
```

忽略根目录下的/config文件夹下的所有文件。

```
config/*
```

忽略任意路径下的/config文件夹下的所有文件。

```
/config/*!README.md
```

忽略根目录下的/config文件夹下除README.md以外的所有文件

```
/config/README.md
```

忽略根目录下的/config文件夹下的README.md文件

# 3：版本控制

release版本，有助于保存阶段性成果。