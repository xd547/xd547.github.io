---
layout: post
title: "Shadowsocks sock5 代理配置－服务端篇"
date: 2014-03-28 15:41
comments: true
categories: ['各种各种']
---
0.废话不说，直接上，哦不是，直接用ssh连上服务器
1.检查python的版本，需要2.6或者2.7，3.0以上没有测试过
```bash
python --version
# Python 2.7.3
```
2.安装所需要的python库以及pip
```bash
# 之前可以先 sudo apt-get update
sudo apt-get install python-gevent python-m2crypto python-pip
```
3.安装shadowsocks
```bash
sudo pip install shadowsocks
```

4.用你喜欢的编辑器创建config.json

```javascript
{
    "server":"你的服务器ip", // 你的服务器ip
    "server_port":8388,    // shadowsocks监听的端口，建议修改，如果服务器有防火墙记得打洞
    "local_port":1080,     // 服务端不重要
    "password":"barfoo!",  // 加密的密码
    "timeout":600,
    "method":"aes-256-cfb" // 加密方式，前面已经安装了python-m2crypto，这里可以选择这个
}
```
无注释版
```javascript
{
    "server":"你的服务器ip",
    "server_port":8388,
    "local_port":1080,
    "password":"barfoo!",
    "timeout":600,
    "method":"aes-256-cfb"
}
```
5.开个screen，开启shadowsocks
```bash
screen -S s
# 注意有3个s
ssserver -c config.json
```
