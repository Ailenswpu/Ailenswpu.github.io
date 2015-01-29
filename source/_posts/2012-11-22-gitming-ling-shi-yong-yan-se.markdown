---
layout: post
title: "git命令使用颜色"
date: 2012-11-22 11:05
comments: true
categories: git
---
最近经常使用git，无免产生一些遐想，在使用git命令的时候需要配置一些颜色，具体的步骤如下：<br/>
1.打开所在项目的.git文件，然后用文本编辑器（emacs,vim等）打开config文件。
2.在文件最后面加上那个<br/>
    [color]
     branch = auto
     diff = auto
     status = auto
    [color "branch"]
     current = yellow reverse
     local = yellow
     remote = green
    [color "diff"]
     meta = yellow bold
     frag = magenta bold
     old = red bold
     new = green bold
    [color "status"]
     added = yellow
     changed = green
     untracked = cyan
