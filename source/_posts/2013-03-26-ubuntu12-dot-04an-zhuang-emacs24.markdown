---
layout: post
title: "ubuntu12.04安装emacs24"
date: 2013-03-26 16:04
comments: true
categories: emacs
---
因为最近看了一些文章，emacs24增加了包管理机制，因此很方便安装一些插件，具体参考[emacs](http://www.gnu.org/software/emacs/manual/html_node/emacs/index.html)，下面主要介绍一下emacs24的主要安装方法:
在终端中输入如下代码就可以直接安装:
    sudo add-apt-repository ppa:cassou/emacs
    sudo apt-get update
    sudo apt-get install emacs-snapshot
    emacs-snapshot
