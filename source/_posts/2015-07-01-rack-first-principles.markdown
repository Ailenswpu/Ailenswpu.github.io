---
layout: post
title: "Rack: First Principles"
date: 2015-07-01 21:38:42 +0800
comments: true
categories: Rack
---
从事rails开发接近3年,"rack middleware"是一个非常熟悉的名词,那么到底什么是rack？接下来这几篇文章将着重讨论一下,今天先讨论一下rack。

### 1.What is rack?
官方的解释为:

  Rack provides a minimal, modular, and adaptable interface for developing web applications in Ruby. By wrapping HTTP requests and responses in the simplest way possible, it unifies and distills the API for web servers, web frameworks, and software in between (the so-called middleware) into a single method call.  

简单地说就是rack封装了http的reques和response。通过rack app能够非常简单地使用封装过后的request和response。然后通过给server提供api,frameworks,middleware和一个call方法就能够运行一个web程序。

### 2.A single method call?
一个rack app就是一个能够响应call方法的object,call方法包含一个env的参数,这个方法返回一个数组包含三个值:status、header、body。
由于rack提供api给server，因此call方法的参数env就是server环境，比如一些key-value的环境变量，可以参考[这里](http://www.rubydoc.info/github/rack/rack/master/file/SPEC#The_Environment)。  
至于为什么返回三个参数，HTTP请求通常需要的response需要三个参数：  

* status: http状态码.参考[这里](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)
* headers: [HTTP Headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)
* body: http response正文，通常是json或者html页面   

### 3. First rack application  
我们来开发一个最简化的rack app  

安装rack   
```ruby
gem install rack
```

接着创建文件，写入  

```ruby
# rack_app.rb
require 'rack'

rack_app = Proc.new do |env|
  [200, {'Content-Type' => 'application/json'}, ["{'response':'OK'}"]]
end

Rack::Handler::WEBrick.run rack_app

ruby rack_app.rb
```
可以看到控制台输出为：  
```ruby
➜  Desktop  ruby rack_app.rb
[2015-07-01 22:00:47] INFO  WEBrick 1.3.1
[2015-07-01 22:00:47] INFO  ruby 2.1.2 (2014-05-08) [x86_64-darwin13.0]
[2015-07-01 22:00:47] INFO  WEBrick::HTTPServer#start: pid=4275 port=8080
```
通过`Rack::Handler::WEBrick.run rack_app`这句代码调用了rack内置的WEBrick服务器，通过浏览器访问:
[http://locahost:8080](http://locahost:8080)就可以访问。  

另外一种方式通过`rackup`命令同样可以启动这个服务。  

```ruby
# rack_app.ru
app = Proc.new do |env|
  [200, {'Content-Type' => 'application/json'}, ["{'response':'OK'}"]]
end

run app
```
要注意使用rackup必须使用以.ru为后缀的文件，通过调用run方法告诉rackup调用call方法,run方法传一个参数如上面的app。
然后运行:  
```ruby
rackup rack_app.ru

可以看到
Puma 2.11.1 starting...
* Min threads: 0, max threads: 16
* Environment: development
* Listening on tcp://localhost:9292
由于我本地安装的是puma服务器，覆盖了内置的WEBrick
```
### 4.总结

rack在整个ruby体系中web应用中占据着不可替代的作用，很多框架都是基于rack开发的，因此了解rack的原理非常重要。
