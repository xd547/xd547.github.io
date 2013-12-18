---
layout: post
title: "在ubuntu上安装较新版本的nodejs"
date: 2013-12-18 14:21
comments: true
categories: ["linux", "ubuntu", "web server"]
---
```bash
sudo apt-get update
sudo apt-get install -y python-software-properties python g++ make
sudo add-apt-repository -y ppa:chris-lea/node.js
sudo apt-get update
sudo apt-get install nodejs
```
因为用到 ``add-apt-repository`` 可能需要安装 `` software-properties-common ``    
```bash
sudo apt-get install software-properties-common
```


来源：[Installing Node.js via package manager](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager#ubuntu-mint-elementary-os)
