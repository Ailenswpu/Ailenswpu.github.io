---
layout: post
title: "rails rake 版本问题"
date: 2012-11-19 15:49
comments: true
categories: rails
---
#rails rake 版本问题
之前一直困扰我们的rake版本的问题，今天查找到了解决方法。
   
   通常情况下，如果我们电脑上同时装了不同版本的rake时，运行rake命令时会出错，如：

    rake db:migrate
    rake aborted!
    You have already activated rake 0.9.2, but your Gemfile
    requires rake 0.9.0. Consider using bundle exec.

    (See full trace by running task with --trace)

     之前一直不理解它所说的“ Consider using bundle exec.” 究竟是什么意思，
今天看到一个示例，真是恍然大悟，具体使用方法是在运行的命令前添加
 bundle exec，如上面的命令，我们写成：
bundle exec rake db:migrate

这样就OK了。