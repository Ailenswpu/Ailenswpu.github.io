---
layout: post
title: "rails_admin跟devise的配合使用"
date: 2013-03-24 21:43
comments: true
categories: rails
---
最近在做项目的使用经过同学的推荐使用了[rails_admin](https://github.com/sferik/rails_admin)里面自带了[devise](https://github.com/plataformatec/devise)，rails_admin是推荐很多人推荐的后台管理，其好处就是简单配置，对于CRUD系统可能很简单，但是对于一些特殊需求的可能就难以满足要求了，不过为了满足大多数人的要求，加上自己的一个总结，具体阐述一下rails_admin的使用方法:
1. 首先根据[Installation](https://github.com/sferik/rails_admin#installation)安装基本的配置环境,包括devise。
2. 接着需要对config/initializers/rails_admin.rb进行配置:
    RailsAdmin may need a way to know who the current user is]
    config.current_user_method { current_admin } # auto-generated
这一段代码主要是对于当前需要验证的用户的识别，可以是current_MODELNAME,很多系统的管理员是单独的model例如admin，那么就改成current_admin。
     Include specific models (exclude the others):
     config.included_models = [model1,model2,model3,....]
这个主要是对于需要后台进行管理的model的配置，如果配置了一个model以后，会有相应model的CRUD，同时会有导出等功能.
3. 最后说一下个人使用rails_admin的常用一些配置跟DSL具体参见[wiki](https://github.com/sferik/rails_admin/wiki)
首先就是对于model中的例如性别、xx类型，在数据库中都是存储的int类型，但是在页面中需要用一个清晰明了的汉字来表示，那么怎么做呢，rails_admin提供了一种单独的DSL([DSL两种配置方法](https://github.com/sferik/rails_admin/wiki/Railsadmin-DSL)):
当model中有一个xx(如:ad_type)属性在页面中需要使用select来进行转换，参见[Enumeration](https://github.com/sferik/rails_admin/wiki/Enumeration),定义一个方法如下:
    def ad_type_enum
    {首页广告: 1, 弹出层广告: 2 , 关于我们: 3 ,关于背景: 4}
    end
那么在视图中就会把hash自动转换成select，这样很好的解决了用户界面跟数据之间数据类型的问题。
其次对于更详细的用法需要参考上面的wiki的链接。
### 补充说明
在使用devise和rails_admin的时候很多时候对于不同scope的devise的页面模板不一样，需要配置scope：
     #在config/initializers/devise.rb中修改如下
     config.scoped_views = true
