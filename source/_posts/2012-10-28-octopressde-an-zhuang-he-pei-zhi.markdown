---
layout: post
title: "Octopress的安装和配置"
date: 2012-10-28 20:36
comments: true
categories: Octopress
---
   最近一时脑子发热想起了写博客，确实如此，很多时候发觉自己需要记录一些东西。因此按照rubychina上面一位高手的文章终于搭建成功了，并且成功用Github Pages部署到github上面去，博客地址为：http://ailenswpu.github.com.下面就具体说说我搭建博客的步骤，因为是第一次搭建，所以遇到了不少弯路。  首先Octopress源代码是根据Jekyll，采用rails来编写，最新的需要的配置环境如下：ruby1.9.3，rails3.X，git，ssh等。很多linux已经安装好了。<br/>
  具体安装rails的过程请看：http://blog.csdn.net/renkai611919/article/details/7792252<br/>
   具体的安装过程看：http://happycasts.net/episodes/36这篇文章里面的视频。<br/>
   需要加以说明的是如果你的github的用户名中有大小写字母的时候一定要严格按照大小写，否则在rake deploy的时候不能push上去（这个问题当时纠结了我好久）。
