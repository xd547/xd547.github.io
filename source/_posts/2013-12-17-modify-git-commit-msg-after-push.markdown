---
layout: post
title: "修改git push 后的commit message"
date: 2013-12-17 16:17
comments: true
categories: ["坑"]
---
背景：手快把一个有拼写错误的commit message给push了    
如何：
```bash
git commit --amend #修改commit message
git push -f #强制提交，如果不强制提交会被拒绝
```

