---
layout: post
title: "goagent上twitter的错误"
date: 2013-03-26 22:11
comments: true
categories: blog
---
今天学习了一下linux下面使用goagent来进行翻墙浏览twitter、facebook等。具体参照[IntallGuide](https://code.google.com/p/goagent/wiki/InstallGuide)。不过出现了打开twitter等网站证书的问题，参照了[ruby-china](http://ruby-china.org/topics/3114)，这个导入了证书还是没用。
汗颜，搞了好久，最后终于在[googlecode](https://code.google.com/p/goagent/issues/detail?id=775)知道到了如下方法:
     使用中发现很多https网站弹出证书警示，需要将local/ca.crt证书导入，下面简单介绍ubuntu下chrome证书的导入方法。
     安装libnss3-tools:sudo apt-get install libnss3-tools
     然后导入证书(这里假定证书ca.crt位于"/goagent/local/ca.crt"):certutil -d sql:$HOME/.pki/nssdb -A -t "C,," -n GoAgent -i '/goagent/local/ca.crt'
