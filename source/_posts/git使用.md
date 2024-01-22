---
title: git使用
top: false
cover: false
toc: true
mathjax: true
date: 2024-01-16 17:04:07
password:
summary: 
tags:
- git
- 文件系统
categories: 工具篇
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

##### git reset

> 麻了，先写的git reflog命令，然后实践的git reset，但因为选择了--hard选项，把未提交的这个文件的修改搞没了，真就现学现用了😳

`描述：`修改HEAD指针的指向，谨慎选择soft/hard参数

`用法：`

```sh
git reset [--mixed | --soft | --hard | --merge | --keep] [-q] [<提交>]
```

`示例：`

```sh
3cb5bf1c (HEAD -> master, origin/master) HEAD@{0}: reset: moving to 3cb5bf1c
0cf04a97 HEAD@{1}: commit: 测试rest
3cb5bf1c (HEAD -> master, origin/master) HEAD@{2}: pull: Merge made by the 'ort' strategy.
```

##### git revert

`描述：`从已push的记录中把某条commit的修改内容干掉。术语来说就是：回退已经push的commit，但HEAD指针不会回退，而是生成一条新的commit记录，HEAD指针后移

`用法：`

```sh
git revert [<选项>] <提交号>... 或：git revert <子命令>

    --quit                终止反转或拣选操作
    --continue            继续反转或拣选操作
    --abort               取消反转或拣选操作
    --skip                跳过当前提交并继续
```

`示例：`

```
git revert 某条commitHas
```

##### git cherry-pick

`描述：`一个挺有意思且很好用的命令，具体功能：复制某分支的某条commit所做的变更到当前分支

`用法：`

```sh
用法：git cherry-pick [<选项>] <提交号>...
  或：git cherry-pick <子命令>

    --quit                终止反转或拣选操作
    --continue            继续反转或拣选操作
    --abort               取消反转或拣选操作
    --skip                跳过当前提交并继续
    
git cherry-pick commit1 commit2 #复制多条commit
git cherry-pick commit1^..commit2  #区间复制
```

`示例：`

```sh
git cherry-pick commit1 commit2 #复制多条commit
```

##### git reflog

`描述：`列出引起HEAD指针变动的git命令记录，包含commitHash值

`用法：`

```sh
git reset [--mixed | --soft | --hard | --merge | --keep] [-q] [<提交>]
```

`示例：`

```sh
git reflog
3cb5bf1c (HEAD -> master, origin/master) HEAD@{0}: reset: moving to 3cb5bf1c
0cf04a97 HEAD@{1}: commit: 测试rest
```

### 五、延伸：git设计

说到git的设计，其实有很多可说，想说的，当年刚入职时分享git的原理可是写了1000字有余呢。可那是专属那个年纪的it浪漫，如今，书信不再，车马渐快。精力珍贵，我仅简要介绍下：

对git系统首先要建立一个重要认知：git面向应用时是代码管理工具，面向过程时是文件系统(.git目录)。

文件系统自然是以文件对象为主体展开的，当我们创建一个文件，将修改添加到index区，git就会为文件中的内容根据SHA算法生成一个哈希值作为文件的引用，操作文件后也会修改文件的引用值，而另开分支修改则是改变了HEAD指针指向。

就简要说这些，show you code:

```sh
1. 假设我们进入到一个新目录，其中有一个 README 文件。此时暂存区为空，提交历史为空，HEAD 引用指向未创建的 master 分支。
2. 现在我们想提交该文件，首先需要通过 git add 将其添加到暂存区。此时 Git 将在 .git/objects 目录中以该文件的内容生成一个 blob 对象，并将 blob 对象的信息添加到 .git/index 文件中。
3. 接着运行 git commit ，它会取得暂存区中的内容生成一个 tree 对象，该 tree 对象即为工作区文件的永久快照，然后创建一个指向该 tree 对象的提交对象，最后更新 master 指向本次提交。
4. 假如我们在工作区编辑了文件，Git 会将其与暂存区现有文件快照进行比较，在 git add 了更改的文件后，根据文件当前内容生成新的 blob 对象并更新 .git/index 文件中的引用 ID。git commit 的过程与之前类似，但是新的提交对象会以 HEAD 引用指向的提交作为父提交，然后更新其引用的 master 指向新创建的提交。
5. 当我们 git checkout 一个分支或提交时，它会修改 HEAD 指向新的分支引用或提交，将暂存区填充为该次提交的文件快照，然后将暂存区的内容解包复制到工作区中。
```

再附上其中一部分的操作记录：

```shell
zyz@bogon  ~/Desktop/myblog/hexo-theme-yuzhe/.git/objects  pwd
/Users/zyz/Desktop/myblog/hexo-theme-yuzhe/.git/objects
 zyz@bogon  ~/Desktop/myblog/hexo-theme-yuzhe/.git/objects  git cat-file -t 60fc2990
commit
 zyz@bogon  ~/Desktop/myblog/hexo-theme-yuzhe/.git/objects  git cat-file -p 60fc2990
tree 13ddbeacacc3f9628813a85a4361556e893a8559
parent 94ba448f2e678a4ec4ad632bb180a856ac708402
author zhangyuzhe <2824353085@qq.com> 1705499171 +0800
committer zhangyuzhe <2824353085@qq.com> 1705499171 +0800

fix:修改信息
```

### 六、参考目录

https://waynerv.com/posts/git-undo-intro/
