---
title: '基于hexo框架、mastery主题的个人博客搭建[小白教程]'
top: false
cover: true
toc: true
mathjax: true
date: 2021-07-21 15:21:53
password:
summary:
tags: 
	- hexo
	- git
	- mastery
categories: 个人博客
---

## 一、前言

借鉴了大佬们的个人博客搭建指南后，我得以有了互联网时代属于自己的小天地。

但在搭建过程中，我发现了大多数的博客搭建博文对于像我这样的前端小白其实是不友好的，它们往往先入为主的陈述Hexo搭建的优势，却忽略了从静态的HTML页面变迁至Hexo的过程，试想一个连Hexo都没听过的人，一上来就是一堆陌生的名词，自然会望而却步。

而在接下来的篇幅下，我会引导小伙伴们主动思考，从一个最基础的Idea出发，一步步点亮这个火光，最终构建出以Hexo为核心的框架。

## 二、Idea

说到个人博客，你可能会脑海中浮现这样简洁而清新的界面：

![image-20210721092923885](https://gitee.com//future727/imgs/raw/master/zyz_img5/image-20210721092923885.png)

抑或这样温馨且中二：

![image-20210721092956147](https://gitee.com//future727/imgs/raw/master/zyz_img5/image-20210721092956147.png)

但不论那种类型的页面，当你轻击右键，查看网站源代码时，发现大家殊途同归，最终都被渲染为HTML页面，再具体一点说：

### 1.0版本

首先我们输入博客域名，服务器为我们展示静态的html网页，但我们很快发现每个页面在布局上都是相似的，我们将这样的布局称为主题，它们与内容无关；而另一部分即是围绕内容展开的，比如：内容所属的作者、标题、分类等等

说到这，聪明的你一定会想到，`最终的HTML页面需要由通用的布局与不同的内容组成`

### 1.1版本

既然布局是通用的，那用HTML写显然`聚合不到一起`，需要有一种`模板语言`只将通用的部分写一遍，最终交给服务器聚合,并解析成html页面。

EJS就是满足我们需求的模板语言，我们先来简要的看下它的介绍：

> EJS 是一套简单的模板语言，帮你利用普通的 **JavaScript 代码**生成 HTML 页面。EJS 没有如何组织内容的教条；也没有再造一套迭代和控制流语法；有的只是普通的 JavaScript 代码而已。

它的语法也比较简单：

```ejs
<ul>
  <% users.forEach(function(user){ %>
    <%- include('user/show', {user: user}); %>
  <% }); %>
</ul>
```

那EJS代码如何被解析为HTML页面呢？服务器显然需要一种可以运行javaScript代码的环境，这就引出了Node.js:

> Node.js 是能够在服务器端运行JavaScript 的开放源代码、跨平台运行环境。

### 2.0版本

现在我们完全可以在装有node.js环境的计算机上跑EJS的代码，并让它生成静态页面，但要别人可以访问到我们的页面，显然还需要部署到服务器上，但是腾讯云/阿里云好像都价格不菲，能不能既免费，还把项目给部署了？

github为我们提供了白嫖的机会~

![image-20210720155337760](C:/Users/zyz/AppData/Roaming/Typora/typora-user-images/image-20210720155337760.png)

哇，这样我们只需新建一个符合规范的仓库就搞定了，果然很香。但后期我们如何维护本地仓库与远程仓库呢？

使用**版本控制工具**！！！版本控制工具有很多，选择哪一个呢？

当然是git咯，它和其他版本控制工具有着这样的差异：

- [x] 直接记录快照，而非差异比较

这样说比较晦涩，但它的另一个好处确实非常实在：

> **近乎所有操作都是本地执行**

用过SVN的小伙伴应该体会过那种`被网速支配的恐惧`，而Git完全没有这方面的顾虑！

### 3.0版本

任何一个系统的设计，都应该考虑到丰富的`集成性与可扩展性`，如果到这里戛然而止，那我们只是做出一个生硬的页面而已，如果要在基础上添加一个评论模块，或是更换一个主题，整个系统维护起来非常笨重。

到这里，我们的主角Hexo登场了：

![image-20210720160710051](https://gitee.com//future727/imgs/raw/master/zyz_img5/image-20210720160710051.png)

这样一个**一站式的解决方案才是设计的最终归宿**。至此，我们对于一个框架应该具有什么组件已经了然于胸，开始动手实践吧——

## 三、搭建过程

### 1.下载Node.js

这里没什么特别的要求，下载一个长期稳定，不高不低的版本即可：

![image-20210720161140877](https://gitee.com//future727/imgs/raw/master/zyz_img5/image-20210720161140877.png)

注：**千万别下载最新版本**，因为其他组件可能还没有对它提供支持，不适配导致的结果可能会是莫名其妙的报错。

安装好以后，在命令提示符中输入`node -v`，如果出现版本号，就安装成功啦。

接着输入`npm -v`,验证包管理工具是否可用，使用过linux系统的小伙伴在安装软件时，就会体会到npm的便利。

下载安装包时，npm会请求它的镜像源，如果这个镜像源中没有我们想要的包，就需要切换镜像源，`nrm就是来保证快速切换的`。

nrm的使用可以参考这篇博文：https://segmentfault.com/a/1190000017419993

在这里，我们首先在终端运行npm install -g nrm命令即可安装nrm，接着使用nrm ls命令列出可选择的源，这里推荐使用淘宝镜像源：



### 2.安装git

博主是一路next的，只需要保证可以使用`命令行打开`即可：

![image-20210720161337100](C:/Users/zyz/AppData/Roaming/Typora/typora-user-images/image-20210720161337100.png)

安装完成后，命令提示符中使用`git --version`命令验证

### 3.创建git pages仓库

![image-20210720164923978](https://gitee.com//future727/imgs/raw/master/zyz_img5/image-20210720164923978.png)

新建仓库后，点击仓库处的setting选项：

![image-20210720165118917](https://gitee.com//future727/imgs/raw/master/zyz_img5/image-20210720165118917.png)

找到下面的github pages选项：

![image-20210720165156373](https://gitee.com//future727/imgs/raw/master/zyz_img5/image-20210720165156373.png)

再设置一通：

![image-20210720165456776](https://gitee.com//future727/imgs/raw/master/zyz_img5/image-20210720165456776.png)

### 4.安装Hexo

在合适的地方新建一个文件夹(编程党建议这样：`/develop/blog/`),用来存放我们的博客文件。

在该目录下右键点击`Git Bash Here`，打开git的控制台窗口，以后我们所有的操作都在git控制台进行，笨重的命令提示符可以告一段落了。

输入`npm -i hexo-cli -g`安装hexo，如果有报错，敬请无视。安装完后输入`hexo -v`验证是否安装成功。

接下来就要初始化我们的网站，输入`hexo init`初始化文件夹，接着输入`npm install`安装必备的组件。

这样本地的网站配置也弄好啦，输入`hexo g`生成静态网页，然后输入`hexo s`打开本地服务器，然后浏览器打开[http://localhost:4000/](http://localhost:4000/，)就可以看到我们的博客啦：

### 5.连接Github与本地

要使用git管理本地仓库与远程仓库，就需要建立连接，为了保证连接的安全性，使用SSH进行加密：

> Secure Shell是一种加密的`网络传输协议`，可在不安全的网络中为网络服务提供安全的传输环境。SSH通过在网络中创建`安全隧道`来实现SSH客户端与服务器之间的连接。

- [x] 注：SSH与SSL的区别：
  1. SSH是加密的shell
  2. SSL是通信链路的附加层

这里**以我的github账号为例**，在本地博客根目录下打开git bash,然后输入以下命令：

```bash
git config --global user.name YuZhe727
git config --global user.email zhangyuzhe727@gmail.com
```

然后生成密钥SSH key：

```bash
ssh-keygen -t rsa -C "zhangyuzhe727@gmail.com"
```

在需要输入时，干净利落地来个回车，三个回车就OK了，按照提示，生成的密钥是放在这里的：

![image-20210720171700663](https://gitee.com//future727/imgs/raw/master/zyz_img5/image-20210720171700663.png)

- `id_rsa`: 使用哈希算法rsa生成的私钥；
- `id_rsa.pub`: 生成的公钥
- `known_hosts`: 通信双方的ip

显然这里使用的是非对称加密，安全性更好，将生成的公钥复制，放在远程仓库端，具体操作：
打开github，在头像下面点击settings，再点击SSH and GPG keys，新建一个SSH，名字随便，再将复制的公钥粘贴进去即可。

然后输入`ssh -T git@github.com`验证是否连接成功，出现用户名就说明成功啦：

![image-20210720172854704](https://gitee.com//future727/imgs/raw/master/zyz_img5/image-20210720172854704.png)

最后在根目录下的配置文件注明`以怎样的方式将本地仓库部署到远端仓库的哪个目录下`就OK了。

------

### 6.梦开始的地方

至此，一个最基础的基于Hexo的博客就搭建完成了，我们来看下它的目录结构：

![image-20210720184026599](https://gitee.com//future727/imgs/raw/master/zyz_img5/image-20210720184026599.png)

- `.deploy_git:`将被部署到远端仓库的文件目录，输入`hexo d`命令后生成该目录；
- `.git:`声明本地仓库与远端哪个仓库的哪个分支相关
- `node_modules`：npm安装包的存放目录；
- `public`：向用户展示的静态文件，输入`hexo g`命令后，由source目录自动生成，包含css,js,html等用于渲染的文件和markdown格式的数据文件
- `scaffolds:`描述页面内容的首部信息，也就是样式文件;
- `source`：用户资源目录，我们写的文章就是放在_post目录下的；
- `themes:`主题，可根据个人偏好更改的部分，只需要在外部的_config.yml中显式声明使用的主题名即可.
- `.gitignore:`在使用git部署时哪些文件应该被忽略，以减轻上传负担
- `_config.yml:`最重要的地方，在该配置文件中设置一些全局信息
- `package-lock.json&package.json:`使用npm安装包时生成的文件，你可以在里面看到所安装包的版本情况。

在博客根目录下**hexo g & hexo s hexo d**三连后，输入`github用户名.github.io`，即可看到一个相貌平平的页面~

不如我们来换个炫酷的主题吧：

![image-20210720190529308](https://gitee.com//future727/imgs/raw/master/zyz_img5/image-20210720190529308.png)

权衡之下，选择了**闪烁之狐大佬**的博客主题：

![image-20210720190808008](https://gitee.com//future727/imgs/raw/master/zyz_img5/image-20210720190808008.png)

## 四、个性化设计

在themes目录下执行`git clone git@github.com:blinkfox/hexo-theme-matery.git`命令，发现晚上的网速着实感人。分析一手，应该是发起DNS查询的时候绕了一大圈，解决方式：在本机`操作系统缓存host文件`中加入两条记录：

![image-20210720192925773](https://gitee.com//future727/imgs/raw/master/zyz_img5/image-20210720192925773.png)

![image-20210720193017067](https://gitee.com//future727/imgs/raw/master/zyz_img5/image-20210720193017067.png)

重新尝试，速度直接起飞，果然实践是检验真理的唯一标准。

------

接着在全局配置文件中设置主题：

![image-20210720200803910](https://gitee.com//future727/imgs/raw/master/zyz_img5/image-20210720200803910.png)

然后三连，展示的页面是这样的：

![image-20210720200953438](https://gitee.com//future727/imgs/raw/master/zyz_img5/image-20210720200953438.png)

逐个点击后，发现有的页面展示不出来，这样大致有了一个修正的方向，然后在去别人的博客逛逛，发现了一些炫酷的个性化设计：

1. 雪花特效；
2. 首页音乐
3. 站长提交；
4. SEO优化
5. 图片水印
6. 二级目录

这些暂时都作为待更新内容，让孩子先把提前批过了~

------

> 山高路远，静水深流