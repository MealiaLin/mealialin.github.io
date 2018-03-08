---
layout: post
title: 基于GitHub Pages+Jekyll搭建个人博客
date: 2018-01-15 00:00:00 +0300
description:  # Add post description (optional)
img: background-cover.jpg # Add image post (optional)
tags: [jekyll] # add tag
---

### 一、概述 ###

> **`Jekyll`** 基于Ruby的静态网页生成系统，采用模板将Markdown(或Textile)文件转换为统一的网页
>
> **`GitHub Pages`** 免费的静态站点，三个特点：免费托管、自带主题、支持自制页面和Jekyll

### 二、搭建步骤 ###

**1、建立Github Pages站点**

要求：本地安装git，拥有个人的github账号

首先，在你的github上建立一个以xxx.github.io为命名的代码仓库，其中xxx代表的是你的github账号名，如我的账号名是MealiaLin，则建立的是
[mealialin.github.io](https://mealielin.github.io)，同时在底部**Add .gitigore选择Jekyll**模板，这样Jekyll产生的临时文件，
例如_site目录就不会添加到源代码管理中，当然你也可以以后手动配置

建立完代码仓库后，将代码仓库克隆到本地

```
$ git clone https://github.com/xxx/xxx.github.io.git
```

然后在本地的代码仓库里面创建一个测试页面并推送到github的代码仓库

```
$ cd xxx.github.io
$ echo "Hello World" > index.html
$ git add --all
$ git commit -m "Initial commit"
$ git push -u origin master
```

推送完代码后等一段时间，快的话几分钟，慢的话要二十分钟或者半个小时吧，等github运行编译你的代码，
完了之后在浏览器输入你的项目名称：xxx.github.io，如果一切正常，你就能看到一个显示Hello World的页面

**2、安装配置jekyll**

这一块很麻烦，尤其是基于windows环境的，坑太多，不过这种东西，都是踩着坑过河，习惯了就好，其实整个过程很简单，如果不算被坑的话半天就能
搭完整个环境和项目了，个人来说，我在这一步卡了一天多了，接下来跟大家讲讲的我步骤，千万要记住，不要偷工减料，严格来做，哪一步装错了或者装少了
什么，建议卸了重头再来，毕竟重做比补坑简单多了

  **注意**：所有安装过程中涉及到的目录路径最好不要出现**空格**

（1）安装ruby，根据你的教程，无脑点就行，但是有个注意点，安装的路径中，连同命名，不要出现空格！！！

（2）ruby安装完，会出现有个选项，让你安装MSYS2这个东东，如果没有勾选，后面自己打开cmd，输入“**ridk install**”进行MSYS2的安装，会出现然你选择123，你选3就行。这个过程会下载很多安装包什么的，耐心等待，一定要耐心，要完整装完才行，装好会让你再做一次123选择，这个时候不需要选了，直接enter退出就行了。

（3）安装DevKit，在[官网](https://rubyinstaller.org/downloads/)下载DevKit-mingw64-64-4.7.2-20130224-1432-sfx.exe，然后点击运行，同样，安装目录不要出现空格，可以百度这个相关的安装，还是比较简单的

（4）然后安装RubyGems，从[官网](https://rubygems.org/pages/download)下载压缩包，解压到你想要的目录下，路径不要有空格，然后cmd命令指到这个文件夹下面，输入“**ruby setup.rb**”执行安装，同样也可百度

（5）安装bundler，输入“**gem install bundler**”执行安装

（6）上面的安装基本缺一不可，然后就可以安装jekyll了，执行“**gem install jekyll**”，最后成功了。

**3、搭建项目**

装完jekyll，项目代码就好办了，项目就可以在本地运行了。

（1）在你的本地代码仓库中，创建或使用模板，创建模板使用git bash输入命令“jekyll new **project-name**”就行了，但是创建模板
极其简陋，所以推荐大家使用[第三方主题模板](http://jekyllthemes.org/)，找一个自己喜欢的主题放到你的本地代码仓库就行了。

（2）环境有了，模板也有了，接下来是本地运行代码了。首先，有些模板需要依赖，所以在你的本地代码仓库目录下使用cmd命令运行bundle install
命令安装依赖包，有时候用这个命令不行，会出现缺什么什么的，可以使用“jekyll install xxx”来一个个安装缺的东西。装完以后运行
jekyll serve来启动本地服务器，默认使用4000端口，如果需要更换端口，可以运行“jekyll serve -P $POST”指定其他的端口。

（3）启动服务器了之后，在浏览器输入：“127.0.0.1:4000”就可以进行本地预览了，建议使用webstorm开发，本人超喜欢用ide家的开发工具

**4、项目相关配置文件**

jekyll项目的配置文件主要在于_config.yml里面，模板的配置都会有相关介绍，根据你选的模板在里面进行相应的信息修改就可以将模板转化为你的东西了
