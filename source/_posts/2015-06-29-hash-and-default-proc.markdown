---
layout: post
title: "Hash and default_proc"
date: 2015-06-29 22:11:46 +0800
comments: true
categories: ruby
---
Hash是ruby最常用的数据结构，Hash本身具有很多特性，今天讨论一下[Hash#default_proc](http://apidock.com/ruby/v1_9_3_392/Hash/default_proc)。    
看过这样的代码?
```ruby
result = {}
some_collection.map do |some_entry|
  result[some_entry] = some_expensive_operation(some_entry)
end
result
```
Ok，现在通过Hash#default_proc有一种更加Better的写法。   
```ruby
result = Hash.new do |hash, key|
  hash[key] = some_expensive_operation(key)
end
```
通过Hash.new能够将some_expensive_operation操作推迟到运行时，当hash不存在某个key的时候才开始运算，这将很大提高了效率。
Ok, Nice,再看一个例子：

```ruby
expensive_proc = ->(v) do
  puts "Expensive calculation"
  sleep 1
  [v] * 2
end

expensive_proc_with_cache = Hash.new do |hash, key|
  hash[key] = expensive_proc[key]
end

def repeat_10_times(proc)
  10.times do |i|
    proc[ i % 2 ]
  end
end

repeat_10_times(expensive_proc) # Takes about 10 seconds
repeat_10_times(expensive_proc_with_cache) # Takes about 2 seconds
```

再看一个缓存的例子：   
```ruby
paginated_lookup = ->(index) do
  v = "index[#{index}]"
  {
    index => v,
    (index + 1) => v + '.next',
    (index - 1) => v + '.prev',
  }
end

paginated_lookup_cache = Hash.new do |hash, key|
  hash.merge! paginated_lookup[key]
  hash[key]
end

paginated_lookup_cache[10] => "index[10]"
paginated_lookup_cache[11] => "index[10].next"
```

值得注意的一个坑是:当我们使用`fetch`去取hash值的时候 Hash#default_proc并没有被调用.WTF?如何设计的?