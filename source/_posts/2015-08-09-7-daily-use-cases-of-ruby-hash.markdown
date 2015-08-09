---
layout: post
title: "7 Daily Use Cases Of Ruby Hash"
date: 2015-08-09 22:59:29 +0800
comments: true
categories: ruby
---
这篇博客总结一下ruby中hash在日常开发的7中常用场景。

### 1.Convert JSON to Hash
当我们程序中通过别的API获取到一段JSON String的时候
```ruby
data = "{
  'name': 'Aaron Patterson',
  'screen_name': 'tenderlove',
  'location': 'Seattle, WA'
}"
```
我们应该将JSON String转换成ruby的Hash，这时候我们应该需要借助json这个库:
```ruby
require "json"

profile = JSON.parse(data)
```
IRB输出为:
```ruby
=> {
  'name'=>'Aaron Patterson',
  'screen_name'=>'tenderlove',
  'location'=>'Seattle, WA'
}
```
参考[JSON Parse](http://www.ruby-doc.org/stdlib-2.1.0/libdoc/json/rdoc/JSON.html#method-i-parse)

### 2.Convert Hash To JSON
跟上面互逆的过程，如何将ruby的hash转化为JSON，同样需要json这个库：
```ruby
signups_of_the_week = {
    monday: 2,
    tuesday: 3,
    wednesday: 4,
    thursday: 20,
    friday: 5,
    saturday: 2,
    sunday: 5
}
```
转为json的过程为
```ruby
require 'json'

signups_of_the_week.to_json

输出为
=> "{"monday":2,
    "tuesday":3,
    "wednesday":4,
    "thursday":20,
    "friday":5,
    "saturday":2,
    "sunday":5}"
```
参考[JSON#generate](http://www.ruby-doc.org/stdlib-2.1.0/libdoc/json/rdoc/JSON.html#method-i-generate)

### 3.How To Set Default Value For Nested Hash
当我们有一个嵌套的Hash我们该如何给这个hash赋默认的值
```ruby
contacts = {
  "John" => {
    name: "John",
    email: "john@doe.com"
  },
  "Freddy" => {
    name "Freddy",
    email: "freddy@mercury.com"
  }
}
```
当我们需要取其中的一个元素的时候我们不希望每次都去检查key是否存在，在ruby的hash中像`h[:key1][:key2]`是错误的，那么我们该如何设置默认值呢？

```ruby
contacts = Hash.new do |hsh, key|
  hsh[key] = {
    name: key,
    email: ""
  }
end
#或者
contacts.default_proc = Proc.new do |hsh, key|
  hsh[key] = {
    name: key,
    email: ""
  }
end
```
参考[Hash#new](http://www.ruby-doc.org/core-2.1.0/Hash.html#method-c-new), [Hash#default_proc=](http://www.ruby-doc.org/core-2.1.0/Hash.html#method-i-default_proc-3D)	

### 4.Merge Two Nested Hash
```ruby
wish_list = {
  8 => {
    title: 'The Color of Magic',
  },
  42 => {
    title: 'The Hitch-Hiker"s Guide to the Galaxy',
    price: 5
  }
}

basket = {
  8 => {
    price: 10
  },
  1729 => {
    title: 'Ramanujan:  Twelve Lectures on Subjects Suggested by His Life and Work',
    price: 28
  }
}
```
当我们有上面这个hash我们该如何merge这两个呢？如果我们是rails项目可以通过ActiveSupport来完成
```ruby
require "active_support/core_ext/hash" # not necessary if in Rails

basket.deep_merge(wish_list)
```
当然我们可以自己来实现一个递归的算法：
```ruby
def deep_merge(h1, h2)
  h1.merge(h2) { |key, h1_elem, h2_elem| deep_merge(h1_elem, h2_elem) }
end

deep_merge(basket, wish_list)
```

### 5.Filter Out Some Keys Of a Hash?
```ruby
histogram = {
  monday: 5,
  tuesday: 7,
  wednesday: 10,
  thursday: 18,
  friday: 7,
  saturday: 2,
  sunday: 0
}
```
当我们想删除hash中某一些key时候，如果有AcitveSupport可以:
```ruby
require "active_support/core_ext/hash" # not necessary if Rails

histogram.except(:saturday, :sunday)
```
当然自己实现可以为:
```ruby
def filter(hsh, *keys)
  hsh.reject { |k, _| keys.include? k }
end
```

### 6.Sort Hash By Value
```ruby
scores = {
  "The Lady" => 3,
  "Fate" => 2,
  "Death" => 10
}
#可以通过
leaderboard = scores.sort_by { |_, score| -score }来排序
```

### 7.Find Differences Between Two Hashs
```ruby
entries = {
  1372284000 => 'CVE-2013-4073',
  1368482400 => 'CVE-2013-2065'
}

updated_entries = {
  1385074800 => 'CVE-2013-4164',
  1372284000 => 'CVE-2013-4073',
  1368482400 => 'CVE-2013-2065'
}
#解决方案如下：
new_entries = updated_entries.reject { |k, _| entries.include? k }
```
这是Ruby中hash一些常用的方法，很多时候可以通过这些方法减少冗余代码。