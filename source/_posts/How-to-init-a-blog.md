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

GitHub Pages的服务是在项目仓库的Settings中可以设置，可以将项目源码托管在github中，然后通过设置相应的GitHub Pages功能来创建站点，一般情况下是设置master分支作为GitHub Pages的站点，设置成功后你的项目就可以通过 `https://username.github.io/Repositoryname` 来访问到项目master分支的文件了，非常简单方便。

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

有了Hexo ＋ github pages，就已经可以制作并通过公开链接展示自己的博客了。然而分享出去的内容往往希望能得到读者的评论和反馈，于是我引入了gitalk作为博客的评论插件。

gitalk 是一个利用 Github API,基于 Github issue 和 Preact 开发的评论插件。他的主要特点就是使用Github登录，然后所有的评论都是提到了对应的issue中，非常简单方便。用以下代码就可以生成gitalk插件：

```js
var gitalk = new Gitalk({
  clientID: 'GitHub Application Client ID',
  clientSecret: 'GitHub Application Client Secret',
  repo: 'GitHub repo',
  owner: 'GitHub repo owner',
  admin: ['GitHub repo owner and collaborators, only these guys can initialize github issues'],
  id: location.pathname,      // Ensure uniqueness and length less than 50
  distractionFreeMode: false  // Facebook-like distraction free mode
})

gitalk.render('gitalk-container')
```

不过在这之前是需要申请注册GitHub Application的，这样才能拿到上述代码中必须的参数`clientID`和`clientSecret`

## 需要注意的地方

最后，说一下自己在搭建博客的这个过程中都踩过的坑和需要注意的地方吧：

1. 在设置GitHub Pages的时候，github会默认把项目发布到`https://username.github.io/Repositoryname`这个url上供互联网访问，但我更希望能通过自己的域名来访问，所以我就在阿里云上购买了一个域名，除了要在域名解析的时候通过CNAME类型指向到`username.github.io`,还要在GitHub Pages里设置Custom domain，保存自己的域名。需要把这两边的工作都做了才能通过自己的域名访问到自己的博客;
2. 如果设置了自己的域名访问，项目的根目录将发生了变化，由原来的`/Repositoryname/`变成了`/`，所以_config.yml文件中url和root参数配置都要修改成对应的个性化域名和`/`，不然原本的资源路径都会报错；
3. 同样因为设置了自己的域名，对应OAuth Apps中的Homepage URL和Authorization callback URL两个配置项都要修改成自己的域名，不然gitalk会授权失败，不能正常食用；
4. Hexo的部署非常方便，是可以在一个分支上进行写作，在然后部署到另一条分支上（我就是在hexo分支上写作，部署到master分支，对应GitHub Pages设置的分支）。所以千万不要在部署配置中将branch设置填写为自己当前进行写作的分支，否则Hexo会将生成的站点文件推送至该分支下，并且完全覆盖该分支下的已有内容。