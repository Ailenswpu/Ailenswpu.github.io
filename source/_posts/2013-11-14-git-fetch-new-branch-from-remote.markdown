---
layout: post
title: "git fetch new branch from remote"
date: 2013-11-14 21:17
comments: true
categories: git
---
在很多情况下，使用git开发需要创建过个分支，当别人创建新的分支你怎么样才能pull下来，并且正常开发呢？下面几个步奏:    
    git fetch origin
    git checkout --track origin/<remote_branch_name>
这样轻松解决问题。一个不错的参考网站:    
[git参考手册](http://rogerdudler.github.io/git-guide/index.zh.html)