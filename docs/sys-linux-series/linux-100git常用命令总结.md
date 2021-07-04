---
title: git常用命令
date: 2020-06-16 15:27:34
tags:  git
categories: 
- linux
tags:
- shell
---

 ## Git 常用命令

### 初始化本地仓库

```bash
git init
```

###  删除本地仓库

```bash
rm -rf .git
```

### 添加远程仓库地址

```bash
git remote add origin https:xxx/xxx 
// 当使用ssh Key 功能时，需将地址设为git@github.com这个格式 
git remote add origin git@github.com:userName:/xxxx.git 
```

### 推送到远程仓库

```bash
git push origin master
```

### 组合add commit 和 push 三种命令

```bash
git add -A && git commit -m "add windows 命令行 和 git使用笔记" && git push origin master
```

### 本地仓库与远程仓库关联的方法

方法1：

1. 在github上新建一个远程仓库，不要勾选initialize with a ReadMe.
2. 创建本地仓库
3. 推送到远程仓库中。