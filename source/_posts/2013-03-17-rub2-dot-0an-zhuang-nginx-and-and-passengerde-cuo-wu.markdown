---
layout: post
title: "rub2.0安装nginx&amp;&amp;passenger的错误"
date: 2013-03-17 22:32
comments: true
categories: rails
---
因为最近换成ruby2.0所以，按照原来的rubychina的[wiki](http://ruby-china.org/wiki/mac-nginx-passenger-rails)的方法安装的时候碰到了这样的错误:
当运行到`passenger-install-nginx-module`
就会出现如下错误:


        /usr/local/rvm/wrappers/ruby-2.0.0-p0/rake nginx:clean nginx RELEASE=yes
        rake aborted!
        cannot load such file -- rubygems/builder
        /usr/local/rvm/gems/ruby-2.0.0-p0/gems/passenger-3.0.19/build/gempackagetask.rb:12:in <top (required)>'
        /usr/local/rvm/gems/ruby-2.0.0-p0/gems/passenger-3.0.19/build/basics.rb:41:in'
        /usr/local/rvm/gems/ruby-2.0.0-p0/gems/passenger-3.0.19/Rakefile:24:in <top (required)>'
        /usr/local/rvm/gems/ruby-2.0.0-p0/bin/ruby_noexec_wrapper:14:ineval'
        /usr/local/rvm/gems/ruby-2.0.0-p0/bin/ruby_noexec_wrapper:14:in `'
        (See full trace by running task with --trace)


最后找出解决方案:


        git clone https://github.com/FooBarWidget/passenger.git
        cd passenger
        gem build passenger.gemspec
        gem install passenger-3.9.5.rc3.gem (or type your actual built gem name)


最后顺利解决问题
