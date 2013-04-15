---
layout: post
title: "Ruby on Rails 中灵活的应用程序设置（一）"
date: 2013-04-15 19:23
comments: true
categories: "编程"
---
本文介绍的方法以 ActiveRecord 为基础将应用程序的设置存储到数据库中  
为了方便扩展，设置将以 json 的格式保存到数据库  
同时设置是全局的，只需要一条数据记录就可以满足需求，于是用到了 singleton pattern  
**注意**这种处理方法可能会有性能问题  
下面进入正题   
0.创建 AppSetting model  
```bash
rails g model AppSetting
```
1.修改 AppSetting 的 migration
```ruby
create_table :app_settings do |t|
  t.integer :singleton_guard
  t.text :value

  t.timestamps
end

add_index(:app_settings, :singleton_guard, :unique => true) 
```
<!-- more -->
2.下面直接贴整个 AppSetting model的代码  
```ruby app_setting.rb
class AppSetting < ActiveRecord::Base
  attr_accessible :value, :list
  attr_accessor :list

  after_initialize :init
  before_save :covert_format

  validates_inclusion_of :singleton_guard, :in => [0]

  def covert_format
    self.value = self.list.to_json 
  end

  def load_list
    begin
      self.list = JSON.parse(self.value)
    rescue JSON::ParserError => msg
      logger.error("AppSetting: Can not load list!")
      self.list = {}
    end
  end

  def [](key)
    self.list[key]
  end

  def []=(key, value)
    self.list[key] = value
    self.save
  end

  def init
    self.value ||= "{}"
    self.list ||= load_list
    self.singleton_guard ||= 0
  end

  # class method

  # singleton
  def AppSetting.instance
    begin
      find(1)
    rescue ActiveRecord::RecordNotFound
      row = AppSetting.new
      row.singleton_guard = 0
      row.save!
      row
    end
  end

  def AppSetting.[](key)
    AppSetting.instance[key]
  end

  def AppSetting.[]=(key, value)
    AppSetting.instance[key] = value
  end
end
```
3.为了方便 AppSetting 的使用，重载了操作符 '[]', '[]='  
4.使用示例  
```ruby
# set site_name to "example site". AppSetting will save when set the value
AppSetting["site_name"] = "example site"
# get "site_name" and assign it to variable site_name
site_name = AppSetting["site_name"]
```
5.下一篇将介绍如何在 Controler 和 view 中 使用 AppSetting  
参考 [1](http://stackoverflow.com/questions/399447/how-to-implement-a-singleton-model)