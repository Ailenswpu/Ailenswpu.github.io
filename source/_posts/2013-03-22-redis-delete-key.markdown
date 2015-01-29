---
layout: post
title: "redis delete key"
date: 2013-03-22 10:20
comments: true
categories: redis
---
redis中如果需要删除关于某一个key的所有的内容可以运行如下命令：
      redis-cli -n 0 KEYS ub:2011-07-* | xargs redis-cli DEL(ub:2011-07-*是你要删除的某一个key)
