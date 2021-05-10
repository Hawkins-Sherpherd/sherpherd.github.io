---
layout: post
title: 协同作业流程
---

想要协同作业的话，我们应该使用 Git 一类的版本控制工具，而不是直接互传文件。

&nbsp;
&nbsp;

## 为什么使用 Git

Git 相比原始的互传工程文件有相当大的优势：

具有同步功能，每个人都保存并同步代码库，不担心代码的不同步与丢失。

冲突处理机制。

可以回溯历史版本，当问题代码因为审核的疏忽而被合并时，可以回滚。

可以直观地看到团队成员的工作量。

Git 是时下流行的选择，有许多流行的代码托管平台使用 Git （如 Github）。

&nbsp;
&nbsp;

## 开始使用 Git

（以下内容适用于 Windows 操作系统）

首先，访问 [Git 的下载页面](https://git-scm.com/downloads)，下载对应平台的 Git 客户端。

安装 Git。

打开 Git Bash，cd 到一个风水宝地，放代码仓库。

执行这个命令：

```Bash
git clone 此处换成项目地址
```

现在，你拥有了这个项目的代码仓库。

&nbsp;
&nbsp;

## 设置账号

我们使用 Github 作为代码托管平台。

执行如下命令：
```Bash
git config --global user.name 此处填写 Github 用户名
git config --global user.email 此处填写你注册 Github 时的邮箱
```

&nbsp;
&nbsp;

## 提交与同步

当你完成了你的代码后，想要提交，你要先 commit：

```Bash
git commit -m "中间填你提交的代码的作用"
```

如果添加了新的文件，要在 commit 之前执行如下命令：

```Bash
git add .
```

Commit 之后，执行：

```Bash
git pull origin
```

如果有冲突，处理冲突。

Pull 完后，将代码推送出去：

```Bash
git push -u origin main
```
