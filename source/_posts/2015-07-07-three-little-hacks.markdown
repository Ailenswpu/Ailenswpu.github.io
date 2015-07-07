---
layout: post
title: "Three Little Hacks"
date: 2015-07-07 22:02:08 +0800
comments: true
categories: Ruby
---
讨论一下Ruby中用到的三个技巧:
### 1.Digg
遇到复杂的JSON结构的时候我们经常会通过`fetch`去获取值,这样做的好处是当某个值不存在的时候可以添加默认值,比如:  
```ruby
complex_json = {key_1: {key_2: {key_3: "somevalue"}}}
#为了获取key_3的值我们会写
complex_json.fetch(:key_1, {}).fetch(:key_2, {}).fetch(:key_3,{})
```
IT'S BAD!!!   
看一下下面的代码   
```ruby
module Digg
  def digg(*path)
    path.inject(self) do |memo, key|
      (memo.respond_to?(:[]) && (memo[key] || {})) || break
    end
  end
end

Array.send :include, Digg
Hash.send :include, Digg
complex_json.digg(:key_1, :key_2, :key_3)
```

IT'S BETTER NOW!!   

### 2.Symbol respond_to
很多时候我们会用到关于class的switch如下:
```ruby
case a_thing
  #...
when Array, Hash
  #...
when String
  #...
end
```
看起来很ugly,如果我们用respond_to?来测试methods？可以这样写:  
```ruby
case a_thing
when ->(x) { x.respond_to? :fetch }
  #...
when ->(x) { x.respond_to? :split }
  #...
end
```
看起来不错，但是可以更进一步：   
```ruby
module SymbolRespondTo
  def ~@
    -> (o) { o.respond_to?(self) }
  end
end

Symbol.send(:include, SymbolRespondTo)

case a_thing
when ~:fetch
  #...
when ~:split
  #...
end
```

### 3.Better to_s
```ruby
module DefaultToS
  def to_s
    case self
    when ~:name then name
    when ~:title then title
    else super
    end
  end
end

ActiveRecord::Base.prepend(:to_s)
```