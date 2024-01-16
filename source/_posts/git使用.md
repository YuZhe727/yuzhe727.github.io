---
title: git使用
top: false
cover: false
toc: true
mathjax: true
date: 2024-01-16 17:04:07
password:
summary: 工具篇
tags:
- git
- 模块设计
categories:
---

## git使用

### 一、前言

在壳壳子工作一年有余，回头来看，对工具的使用总归差了点意思，首档其冲的就是git了。

对于git，总有种晦涩难明的情绪，这是我在壳壳子做技术分享的第一个主题，当初针对原理、基础使用、高级使用侃侃而谈。现如今，对于git呼之欲出的印象，却仅有git pull、git add几个常用的命令，换言之，到底还是走了能用就行的老路。

这种认知不能算错，但在某些时机，**那些高级的用法、习以为常的git规范、通用的原理往往能让人在棘手问题前游刃有余**。

这种投入少数精力就能实现差异化竞争的领域，其实很值得投入，所以在工作一年半之后的今天，重新拾起落灰的git神技，也是件水到渠成的事情了。

在正式书写之前，有一点仍需要反复告诫自己：

`以前那种精耕细作的博客记录方式已经不适用了，用最少的结构化语言描述最重要的点，才是当务之急`

### 二、背景

在2024年，如果一个开发人员不能熟练使用git，那他显然就是一个没有职业素养的程序员。

git如此重要的原因，是因为其代表了团队的分布式协作模式，这种可**P2P与CS结合的架构方式**，让它既实现了高效协作，也满足集中管理的诉求。

### 三、概述

> git是一个分布式版本控制软件，与集中式的版本控制工具相比，它有着一下优点：
>
> 1. 可离线访问，速度快，因为本地仓库～远端仓库
> 2. `记录快照而非diff`，Git存储的是每个文件的完整内容
> 3. 分支管理功能强大

### 四、具体使用

#### 1. 使用前配置

有状态协议自然需要识别用户身份是否合法：

1. 下载git

2. 使用公司内部用户信息登陆代码平台

3. 本地生成ssh秘钥，将公钥复制到代码平台,在通信时使用非对称加密的方式验证本地的私钥与远端的公钥

   ```sh
   ssh-keygen -t rsa -C "公司内部邮箱地址"
   cat ~/.ssh/id_rsa.pub 
   add ssh key
   ```

4. 配置通信用的用户信息

   ```shell
   git config --global user.name "xxx"
   git config --global user.email "xxx@xx.com"
   ```

#### 2. 用一个例子认识git

![git](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ec7210655b4d4fc4afcd1466d9aa2343~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

场景：拉取一个新项目并修改其中的checkstyle.xml文件

实践：

```sh
#使用https/ssh的方式拉取，通常使用ssh地址
git clone 远端git地址 
#在项目文件的根目录执行命令 交由git管理
git init 
#修改文件  #添加文件到暂存区
git add checkstyle.xml
#不确定改动是否合适，还不到commit的时机，但要看别的代码，遂暂存现场
git stash 
git stash list
git stash pop/apply
#确定改动是合适且是一个阶段的最终结果，遂提交
git commit -m“feat：修改checkstyle.xml文件”
#认为提交信息要更改，遂修改
git commit --amend
#本次做的变更没有意义，认为应该回退到上一个版本 --soft，--mixed和--hard
git reset HEAD 
#修改信息，重新提交，准备部署到远端
git pull
#别人也修改了相同的代码，代码出现冲突 这一步建议使用可视化工具合并
git merge 分支名 
#产品说这个功能不用做了，回退到上个版本 
git revert 版本号
```

#### 3. 奇技淫巧

git reset

git revert

git cherry-pick

git reflog

### 五、延伸：git设计

c语言 指针

