---
layout: post
title: "在Ubuntu上使用nginx官方源安装nginx"
date: 2013-12-17 21:33
comments: true
categories: ["linux", "ubuntu", "web server"]
---
0.添加key
```bash
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys ABF5BD827BD9BF62
```
1.添加到nginx官方源到 `` /etc/apt/sources.list ``
```bash
deb http://nginx.org/packages/ubuntu/ precise nginx
```
2.如果原来安装过nginx，备份相关配置文件后删除nginx及其相关组件
```bash
sudo apt-get purge nginx*
```
3.安装nginx
```bash
sudo apt-get update
sudo apt-get install nginx
```
4.安装成功后可以用-v查看ningx版本
```bash
nginx -v
#nginx version: nginx/1.4.4
```
来源：[How to Setup Rails App With Puma and NGINX](http://ruby-journal.com/how-to-setup-rails-app-with-puma-and-nginx/)