---
layout: post
title: "Five Ruby Methods You Should Be Using"
date: 2015-01-29 21:51:35 +0800
comments: true
categories: ruby
---

1.Object#tap
==========
是否有过当你调用某个对象的方法时候，返回值并不是你想要的？这时候你就需要重新获取对象，并对对象做操作。比如：你想要给存储一系列参数的hash加一个任意值，
你通过调用hash.[]来更新hash，因此你必须明确地返回才能得到更新后的结果，代码如下:  
```ruby
def update_params(params)
  params[:foo] = 'bar'
  params
end
```
代码最后一行的params看上去似乎是多余的。  
因此可以通过(Object#tap)[http://www.ruby-doc.org/core-2.1.5/Object.html#method-i-tap]来简化代码。对象只要调用tap方法，传递一个你需要
执行的代码块，对象调用代码块的后返回自己。因此改进的update_params代码如下:
```ruby
def update_params(params)
  params.tap {|p| p[:foo] = 'bar' }
end
```
Object#tap在很多地方都很有用.有一个简单法则：对象的方法本身不必要返回自己，当你需要时候即可以用Object#tap来替换。  

2.Array#bsearch
============
不知道你们在工作中是否会遇到遍历数组?Ruby的枚举提供了很多寻找方法比如：select,reject和find，这些是我日常工作中经常使用的，但是当数据集很大的时候，我开始
担心遍历这些数组需要耗费很多的时间。  

如果你使用过ActiveRecord来做SQL查询，在ActiveRecord背后封装了很多魔法，因此你必须要把搜索复杂的降到最低。但是很多时候你必须要把所有数据先查询出来然后
才能做进一步工作。比如：当数据库中的数据做了加密的时候，你不能直接通过SQL来做操作。  

在这个时候，我通常思考如何使用算法复杂度最低(大O)的算法来做数据筛选，如果你不知道大O可以参考Justin Abrahms's的[ Big-O Notation Explained By A Self-Taught Programmer](https://justin.abrah.ms/computer-science/big-o-notation-explained.html)
或者(Big-O Complexity Cheat Sheet)[http://bigocheatsheet.com/]。  

基本的原则就是算法的复杂度决定着算法的运行时间的长短，复杂度从小打到依次排序为：O(1), O(log n), O(n), O(n log(n)), O(n^2), O(2^n), O(n!)，因此我们倾向于
使用复杂度低的算法。  

在Ruby中，当对数组做查询的时候，第一反应是使用Enumerable#find，也可以是detect。但是这个方法会遍历整个数组，直到条件匹配。如果最终结果在前面那么还好，如果
结果在数组最后面，那么最终搜索的复杂度为O(n)。  

在Ruby中有一个更快的方法(Array#bsearch)[http://www.ruby-doc.org/core-2.1.5/Array.html#method-i-bsearch], 搜索的复杂度为O(log n)，如果想要查看
Binary Search的工作原理，可以查看(Building A Binary Search)[http://fluxusfrequency.github.io/blog/2014/01/31/building-a-binary-search/]。

下面是从50,000,000数字中搜索特定值的运行时间对比：
```ruby
require 'benchmark'

data = (0..50_000_000)

Benchmark.bm do |x|
  x.report(:find) { data.find {|number| number > 40_000_000 } }
  x.report(:bsearch) { data.bsearch {|number| number > 40_000_000 } }
end

         user       system     total       real
find     3.020000   0.010000   3.030000   (3.028417)
bsearch  0.000000   0.000000   0.000000   (0.000006)
```

正如你看到的，bsearch速度非常快。但是使用bsearch的大前提就是：数组必须是排序好的。这在很多时候限制了bsearch的用途，但是仍然在很多时候会用到，比如：当你在按照
created_at排序好的数据库记录中按时间查询某个记录。

3.Enumerable#flat_map
====================
当处理相关联的数据时候，我们通常需要查询一些不相关一些列数据，然后以嵌套的数组的形式返回。假设你有一个博客，你想查询出某些用户的上个月的所有的博客的评论的作者，那么
代码如下：  
```ruby
module CommentFinder
  def self.find_for_users(user_ids)
    users = User.where(id: user_ids)
    user.posts.map do |post|
      post.comments.map |comment|
        comment.author.username
      end
    end
  end
end
```
你最终有可能得到如下结果：  
```ruby
[[['Ben', 'Sam', 'David'], ['Keith']], [[], [nil]], [['Chris'], []]]
```

但是真正的需求是我想得到的是作者，而不是[]或者nil,因此你可以通过flatten来合并：
```ruby
module CommentFinder
  def self.find_for_users(user_ids)
    users = User.where(id: user_ids)
    user.posts.map { |post|
      post.comments.map { |comment|
        comment.author.username
      }.flatten
    }.flatten
  end
end
```ruby
Ruby中还有一种更好的方法就是使用flat_map：
```ruby
module CommentFinder
  def self.find_for_users(user_ids)
    users = User.where(id: user_ids)
    user.posts.flat_map { |post|
      post.comments.flat_map { |comment|
        comment.author.username
      }
    }
  end
end
```
后面两种并没有太大区别，但是使用flat_map没必要多次调用flatten。

4.Array.new with a Block
========================
一度在我集训的时候，我们导师Jeff Casimir (founder of (Turing School)[http://turing.io/])让我们在一小时时间内去开发一个Battleship，这是一次很好的
面向对象的编程练习，我们需要Rules, Players, Games, and Boards。  
创建Board的表现形式是一个很有趣的练习，在多次迭代之后，我发现了最简单的方式去构建一个8x8的格子:
```ruby
class Board
  def board
    @board ||= Array.new(8) { Array.new(8) { '0' } }
  end
end
```

那么到底发生了什么呢？当你调用(Array.new(n))[http://www.ruby-doc.org/core-2.1.5/Array.html#method-c-new],就会创建一个长度为n的数组：  

```ruby
Array.new(8)
=> [nil, nil, nil, nil, nil, nil, nil, nil]
```

当你传递代码块的时候，就会把代码块中的值加入到数组中：  

```ruby
Array.new(8) { 'O' }
=> ['O', 'O', 'O', 'O', 'O', 'O', 'O', 'O']
```

因此如果你在代码块中传第一个包含8个'0'的数组，那么你就会得到一个8x8的所有元素为'0'的数组。使用Array.new加上代码块，你可以构建出任何大小，任何嵌套的数组。


5.<=>
======

这个如"宇宙飞船"的符号是我在Ruby中最喜欢的结构之一，它在广泛使用于Ruby的内置类中，同时在枚举中也广泛使用。

下面通过整形数值调用<=>来看一下用法，当你运行5<=>5时候, 返回值 0，当你运行4<=>5返回-1，当你运行5<=>4，返回1.因此，如果两个数字一样，返回0，前者小于后者，返回-1，如果
前者大于后者返回1.  

你可以使用<=>来重新定义自己的逻辑，使其只返回0，1，-1. 
 
下面是<=>的一个非常cool的使用场景，来自Exercism， 有一个练习叫做Clock，你必须使用自定义的+和-来调整时钟的小时分钟。当时间超过60分钟的时候就会变得非常复杂，
因为超过60分钟是不合法的数据，因此你必须要加一个小时，同时减去60分钟。  
一个叫dalexj的聪明小伙子的解决方案如下：  
```ruby
def fix_minutes
    until (0...60).member? minutes
      @hours -= 60 <=> minutes
      @minutes += 60 * (60 <=> minutes)
    end
    @hours %= 24
    self
  end
```
<=>符号非常适用于定义你自己对对象的排序，同时可以用于某些数学运算，因为它仅仅返回三个固定的数字。

原文链接为：https://blog.engineyard.com/2015/five-ruby-methods-you-should-be-using

处女翻译贴献给了ruby-china，如果哪里有错误的请大家及时指正。:smile:
