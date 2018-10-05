---
title: yilia主题个性化配置与修改
date: 2018-10-03 20:03:36
tags:
- hexo
- yilia
---

今天折腾了一天，终于把自己的小博客搞起来啦，巨开心～

Hexo框架下有许多好看又简洁的主题，可以去 [Hexo Themes](https://hexo.io/themes/) 查看。

在github上比较出名的是 [NexT](https://github.com/iissnan/hexo-theme-next)，简洁且比较成熟，集成的功能也比较多，也是在 GitHub 上[被 Star 最多](https://github.com/search?o=desc&q=topic%3Ahexo-theme&s=stars&type=Repositories)的一个 Hexo 主题。第二多的是 [hexo-theme-yilia](https://github.com/litten/hexo-theme-yilia)，风格是我比较喜欢的，简洁大方。第三多的是 [hexo-theme-material](https://github.com/viosey/hexo-theme-material)，有卡片式的风格（还有一个很棒的 Material Design 风格主题 [hexo-theme-material-indigo](https://github.com/yscoder/hexo-theme-indigo/tree/card)），还有一个我感觉比较有特色的，叫 [Cactus Dark](https://github.com/probberechts/cactus-dark)，小清新的感觉。

选择恐惧症纠结了很久，本来觉得next主题比较成熟，已经把[文章访问统计](https://notes.doublemine.me/2015-10-21-为NexT主题添加文章阅读量统计功能.html)什么的都配好了，但后面看到了yilia主题巨喜欢就毫不犹豫地又折腾了一遍= =建议大家一开始就选个看对眼的主题，配置主题费时费力还全是脏活 : (

下面记录一下自己安装和配置主题的全过程～

<!--more-->

### 一、安装主题

目前使用的主题是：[yilia](https://link.jianshu.com?t=https://github.com/litten/hexo-theme-yilia)

##### 在hexo的**根目录**下（blog文件夹）克隆主题

```
$ git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```

##### 执行：

```
$ vim _config.yml   
```

##### 将 theme 对应的值进行修改

```
theme: yilia    
```

##### 最后发布：

```
$ hexo clean && hexo g && hexo d
```

就可以查看效果了～

### 二、主题配置

现在主题是更改过来了，但还有许多细节需要处理，比如说你需要修改头像等等。

##### 首先进入到根目录下的 themes\yilia 文件夹，执行

```
$ vim _config.yml
```

##### 配置比较简单，文件夹是以source为根目录，可以在里面新建一个文件夹存放头像等图片，配置完成以后，回到 **根目录**），**按顺序执行命令**就OK啦。

```
$ hexo clean && hexo g && hexo d
```