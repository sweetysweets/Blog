---
title: mac下hexo框架+github pages博客+个人域名配置
date: 2018-10-02 22:23:28
tags:
- hexo
---

想弄博客很久了但懒癌晚期一直以各种借口拖呀拖呀，今天终于逼自己配置了一下这个= =就把这个作为博客的开端，希望自己以后努力记录每一个折磨人的bug，学习笔记，记录成长：）

## 关于Hexo

> Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。

配置好hexo后用本地markdown编辑器编辑博客并渲染上传即可，操作很简单，需要之后在seo（search engine optimization，搜索引擎优化）下下功夫，这个有空就提上日程吧= =

 <!--more-->

## 环境配置及安装

1. Node.js

   [Node.js官网](https://link.jianshu.com?t=https://nodejs.org/en/)进行安装。

2. Git

   Xcode自带Git环境，或者在[Git官网](https://link.jianshu.com?t=https://git-scm.com)进行安装，或者使用`Homebrew`安装：

   ```
   $ brew install git
   ```

3. 安装`Hexo`：

   ```
   $ sudo npm install -g hexo-cli
   ```

4. 在某一个文件夹下初始化hexo并生成

   ```
   $ hexo init blog //这个命令就在blog这个文件夹下安装hexo
   $ npm install
   // 成功之后的目录结构如下
   ├── _config.yml
   ├── package.json
   ├── scaffolds
   ├── source
   |   ├── _drafts
   |   └── _posts
   └── themes$ sudo npm install -g hexo-cli
   ```

命令执行结束后，就可以正式使用`Hexo`了，使用`debug`模式进行查看：

```
$ hexo s --debug
```

访问`http://localhost:4000`就可以看到`Hexo`的默认界面了。

## Github Page

> GitHub Pages is a static site hosting service designed to host your personal, organization, or project pages directly from a GitHub repository.

总之，这玩意就是一个托管在github上的静态网页，现在国内coding、码云也都有相应的page支持，且访问速度更好，以后有需求可以考虑他们。

### 创建仓库

进入`Github`新建仓库，名称格式为`UserName.github.io`，例如`sweetysweets.github.io`。然后将本地`Hexo`的`站点配置文件`的配置仓库修改为当前仓库的`git`地址：

```
$ cd ~/blog/
$ open _config.yml
// 修改repository，注意yml文件的格式冒号后要空一格才能识别
deploy:
  type: git
  repository: https://github.com/sweetysweets/sweetysweets.github.com.git
branch: master
```

### 配置ssh key

这个是可有可无的步骤，有ssh key可以让github访问不需要输入密码，更安全，也可以用http方式访问，具体配置参见[这篇文章](https://www.jianshu.com/p/a422eb2bb8e2)。

### 安装deploy工具

```
$ npm install hexo-deployer-git --save
```

到此就可以正常绑定本地hexo和github page了。

## 日常使用

在`blog`目录下新建文章：

```
$ hexo new "Hello-world"
```

`Hexo`会自动生成`.md`文件在`~/blog/source/_posts`目录下，可以使用各种`Markdown`编辑器进行博客的书写了。之后生成`generate`静态文件后进行部署`deploy`操作，会让你的文章发到`Github`托管的服务器上：

```
$ hexo g -d
//也可以分开来
$ hexo g
//$ hexo s 可以在本地localhost：4000预览效果
$ hexo d   //部署到github上
```

修改主题文件后需要清除缓存

```
$ hexo clean
$ hexo g -d
```

别忘了将修改推送到备份源码仓库

```
$ cd blog
$ git add .
$ git commit -m '...'
$ git pull
$ git push
```

此处我已经配置过源码仓库，具体如下

- 新建一个GitHub仓库，名称Blog
- 进入本地的`Blog`文件夹，执行以下命令创建仓库:

```
$ git init
//设置远程仓库地址，并更新
$ git remote add origin https@github.com:sweetysweets/blog.git
$ git pull origin master
$ vi .gitignore
```

在gitignore中添加`*.log``public/``.deploy*/`,这些是每次编译会重新生成的，不要添加在源码中。

```
//设置默认分支      远端分支 本地分支
$ git branch --set-upstream-to=origin/master master
//然后就可以愉快的推送了
$ git push 或者 git push origin master
```

## 配置个人域名

我是在[freenom](https://freenom.com/)上注册了免费域名，大部分tk、ml、cf、ga、gq结尾的顶级域名是免费的，注册完最长使用一年，但是可以免费无限续期，所以相当于永久免费了～当你选好了一个域名之后需要对其进行解析，在域名管理网页添加解析记录：

【图片= =】

配置好域名解析之后进入`Github`的仓库，`Setting` —> `Options` —> `GitHub Pages` —> `Custom domain`中设置新域名（也可以不设置，直接在本地建CNAME文件）

新建一个`CNAME`文件（不要扩展名）：

```
$ cd blog/source
$ touch CNAME
$ vi CNAME
```

填写你的新域名如`sweets.ml`在其中，这样保证hexo每次编译之后都可以直接使用新域名。

## 备份博客

备份博客思路参考[这篇文章](https://link.jianshu.com?t=https://wmaqingbo.github.io/blog/2017/07/05/%E5%A4%87%E4%BB%BDHexo%E6%BA%90%E6%96%87%E4%BB%B6%E5%88%B0Github/)。用`git branch`来代替`静态页面仓库+源码仓库`的组合，新建一个分支并设为主分支，用来保存源码，`master`分支用来保存静态页面。

我在看到这篇文章之前已经把源码上传到另一个仓库里做备份了，其实没差，就没再做修改，切换电脑之后的操作：

1. 使用git clone https://github.com/sweetysweets/Blog.git拷贝仓库；

2. 在本地新拷贝的Blog文件夹下通过终端依次执行下列指令：

   ```
   //不需要hexo init
   $ npm install hexo
   $ npm install
   $ npm install hexo-deployer-git
   ```

3. 进行日常操作

