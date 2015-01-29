---
layout: post
title: "osx 安装coffeescript"
date: 2013-12-08 17:33
comments: true
categories: CoffeeScript
---
最近学习[CoffeeScript](http://coffeescript.org/),因为电脑是OSX，在安装时候出现了一些问题，现将具体的安装步奏总结如下:   
###1.安装[node.js](http://nodejs.org/) 
下载OSX的安装包[node.js](http://nodejs.org/dist/v0.10.22/node-v0.10.22.pkg),并安装。
###2.  安装coffeescript  
安装好了nodejs后利用node.js的[nmp](https://npmjs.org/)的包管理工具安装coffeescrit,在console中输入:  
     npm install -g coffee-script
console出现：   
   
    npm http GET https://registry.npmjs.org/coffee-script
    npm http 304 https://registry.npmjs.org/coffee-script
    /Users/mac/.npm-packages/bin/coffee -> /Users/mac/.npm-packages/lib/node_modules/coffee-script/bin/coffee
    /Users/mac/.npm-packages/bin/cake -> /Users/mac/.npm-packages/lib/node_modules/coffee-script/bin/cake
    coffee-script@1.6.3 /Users/mac/.npm-packages/lib/node_modules/coffee-script
安装成功。
###3.解决command not found问题:  
因为npm没有把coffee加入到PATH中需要手动加入如下命令:   
	export PATH=/usr/local/share/npm/lib/node_modules/coffee-script/bin/:$PATH
这样就安装成功。
