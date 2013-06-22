---
layout: post
title: "Ruby on Rails 中跨控制器的缓存控制（一）"
date: 2013-06-22 14:55
comments: true
categories: ["编程", "rails"]
---
本篇介绍的方法是一个模型（Model）的多个控制器（Controller）中如何使用 Sweeper   
使用场景：需要后台管理的应用程序中，后台更新信息后，自动清除前台页面的缓存   
下面就是具体过程   
<!-- more -->
0.在 rails 的 config/environments/* 相应的环境配置文件中加入下面代码，这个缓存配置默认在 development test 环境是关闭的，在 production 环境中是开启的   
```ruby
config.action_controller.perform_caching = true
```
1.假设我们有一个名为 Post 的模型   
2.在 app 目录下新建 sweepers 目录，用于存放 sweeper 文件，在该目录下添加 post_sweeper.rb 文件，添加了类常量 CACHE_PATH   
```ruby post_sweeper.rb
class PostSweeper < ActionController::Caching::Sweeper
  CACHE_PATH = "posts_index" # 跨控制器的关键
  observe Post
 
  def after_create(post)
    expire_cache_for(post)
  end
 
  def after_update(post)
    expire_cache_for(post)
  end
 
  def after_destroy(post)
    expire_cache_for(post)
  end
 
  private
    def expire_cache_for(post)
      expire_fragment(PostSweeper::CACHE_PATH) # 清空命名的 expire_fragment
    end
end
```
3.在需要缓存的控制器（Post的前台）中加入如下代码   
```ruby 
class PostsController < ApplicationController
  caches_action :index, :cache_path => PostSweeper::CACHE_PATH
  def index
    # ...
  end

  # 其他 action ...
end
```
4.在需要控制缓存的控制器（Post的后台）中加入如下代码   
```ruby
class Admin::PostsController < ApplicationController
  cache_sweeper :post_sweeper # 指定 sweeper 

  # index show new create edit update destroy actions ...
end
```
5.这样就可以在后台更新 Post 的时候通过 sweeper 来更新前台页面的缓存  
参考 [Caching with Rails: An overview](http://guides.rubyonrails.org/caching_with_rails.html) 

