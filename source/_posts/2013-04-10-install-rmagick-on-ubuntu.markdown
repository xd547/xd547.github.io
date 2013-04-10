---
layout: post
title: "在 Ubuntu 上安装 Rmagick"
date: 2013-04-10 22:47
comments: true
categories:  "编程"
---
Rmagick 是 ruby 中处理图片的开发库，在 Ruby on Rails 中经常用来处理图片的输出和验证码。   
下面就是如何在 Ubuntu 上 安装 Rmagick。  
0.安装 ruby 的运行环境，推荐使用 rvm 来管理和使用 ruby。  
1.打开 shell 输入如下命令  
```bash
sudo apt-get install imagemagick libmagickwand-dev
```
2.继续输入
```bash
gem install rmagick
```
3.验证安装，打开 irb 输入  
```ruby
require 'RMagick'
```
返回 true 就说安装成功  
   
如果在 Ruby on Rails 中使用需要在项目的 Gemfile 中加入
```bash
gem "rmagick"
```
然后
```bash
bundle install
```
至此 Rmagic 就安装完毕了   
另附 Rmagick 的[文档](http://studio.imagemagick.org/RMagick/doc/) 