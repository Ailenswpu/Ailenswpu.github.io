---
layout: post
title: "rails中一些不常用但是很实用的技巧"
date: 2013-04-11 22:26
comments: true
categories:
---
好久没有写博客了、感觉没有提高，先来检讨一下:
### 1.collection && pluck
很多时候我们的代码里面充斥这如下这样的代码：
    published_book_titles = Book.published.map(&:title)
这样的代码当数据量很大的时候效率很低，那么应该改成:
    published_book_titles = Book.published.pluck(:title)
pluck在做查询的时候会指针对特定的列进行查询，而上面一种写法会先把所有数据查询出来然后再进行特定列的筛选。
### 2.find_by
很多时候，如果我们需要查找单条记录代码会是:
    User.where(email: "renkaiswpu@gmail.com",username: "renkaiswpu").first
但是rails设计的时候更常用的是dynamic finder:
    User.find_by_email_and_username 'renkaiswpu@gmail.com' 'renkaiswpu'
如果你使用find_by可以写成这样子：
    User.find_by(email: "renkaiswpu@gmail.com",username: "renkaiswpu"")
    #或者写成
    User.where(email: "renkaiswpu@gmail.com").find_by username: "renkaiswpu""
github的[讨论](https://github.com/rails/rails/pull/5639)可以看出find_by的目的是取代使用method_missing的dynamic finder，find_by的实质就是where().first
### 3.first_or_create && first_or_initialize
    User.where(email: "renkaiswpu@gmail.com").first_or_create
这个正如他的名字，当你在查询一个记录的时候如果存在则返回该记录，如果没有则新建一个，更加简介的做法，采用代码块:
    User.where(email: "renkaiswpu@gmail.com").first_or_create do |user|
       user.name = = 'renkai'
    end
同理，如果使用first_or_initialize 这样的数据不会存储到数据库中。
### 4.find_each & find
当你在遍历成千上万条数据的时候，如果使用each那么会一次性从数据库中把记录查询出来，效率可想而知！！但是当你用find_each的时候那么rails会先加载前1000条数据，其他的会在后续时间继续加载，直到加载完全部放到内存中。
    Book.where(:published => true).find_each do |book|
       puts "Do something with #{book.title} here!"
    end
### 4.rake notes
很多场合下我们会用todolist来提醒自己做一些feature,那么如何使用rake来提醒自己呢，只要在rb文件中加入如下格式的代码：
    #encoding : utf-8
    class Blog
      # TODO: 使用mongodb
      # FIXME: 修改bug
      include Mongoid::Document
      include Rails.application.routes.url_helpers
      field :name, type: String
      field :content, type: String
      attr_accessible :file
      field :file
      mount_uploader :file, FileUploader
    end
那么再使用:
    rake notes
    rake notes:todo
    rake notes:fixme
就可以轻松知道自己的tasklist了。
更加详细的请参考[rails技巧](https://speakerdeck.com/jeg2/10-things-you-didnt-know-rails-could-do)
