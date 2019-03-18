---
title: 搭建自己的博客网站
date: 2019-03-17 08:19:47
tags:
  - 博客
conver: 
---

利用工作之余的零散时间，搭建起了博客，看似简单的操作，但也还是花了一周多的时间去折腾，还真是很多东西是要通过实践才能得到更有触感的认识。所以这次就记录一下自己是怎么去完成这个网站的以及踩过哪些坑。

## GitHub Pages

首先考虑的是自己的博客放在哪里展示博客。本来一开始的时候是想在阿里云或者腾讯云购买一个服务器然后从零搭建一个属于自己的网站的，但是发现等了很久也没有特别有力的优惠活动，而且仔细想想目前也就只是想做自己的一个博客而已，只是静态文件的话其实利用GitHub Pages的功能也是可以了。所以就确定了运用github仓库来对外展示的自己博客的方案。

GitHub Pages的服务是在项目仓库的Settings中可以设置，可以将项目源码托管在github中，然后通过设置相应的GitHub Pages功能来创建站点，一般情况下是设置master分支作为GitHub Pages的站点，设置成功后你的项目就可以通过 https://username.github.io/Repositoryname 来访问到项目master分支的文件了，非常简单方便。

## Hexo

确定了用什么展示html之后，那用什么生成html呢？于是我选择了hexo作为自己博客的框架，官网对hexo的定义是:

> Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。

可见Hexo本身就有博客系统的特性，而促使我选择它的原因主要是：
1. 非常简单易懂的几个命令就能快速初始化，生成和发布项目；
2. 可以用markdown来编写文章内容，简洁高效，省去了很多排版之类的功夫；
3. 有很多漂亮的主题。对于像我一样希望有个个性化的博客网站，但又不想花太多时间自己去制作主题的话，可以在官网的主题专区里去找自己喜欢的主题风格。我在那就发现了好几个挺喜欢的主题，可惜并不能全都要，只好选了最喜欢的那个

Hexo的安装使用非常简单，只需要像官方首页一样执行以下几个命令就可以搭建起博客网页了：

```bash
npm install hexo-cli -g
hexo init blog
cd blog
npm install
hexo server
```

hexo init之后生成的项目的目录结构如下：

```
.
├── _config.yml      // 站点配置文件
├── db.json          // 缓存文件
├── node_modules     // 安装的插件以及hexo所需的一些nodejs模块
├── package.json     // 项目的依赖文件
├── scaffolds        // 模版文件
├── source           // 源文件，用来存放你的文章 md 文件
└── themes           // 主题文件
```

平时的操作只需要执行 `hexo new [layout] <title>`新建一篇文章，然后在source目录下会自动生成对应一个md文件，只需要在文件用markdown编写内容就可以了。完成之后便可以通过`hexo deploy`命令部署到Github Pages设置的分支即可。更多配置等其他内容都在文档中可以找到，这里也不作赘述，会在后面再讲一下这中间需要注意的点和踩到的坑。

## gitalk

有了hexo ＋ github pages，就已经可以制作并通过公开链接展示自己的博客了。然而分享出去的内容往往希望能得到读者的评论和反馈，于是我引入了gitalk作为博客的评论插件。