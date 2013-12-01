---
layout: post
title: "修复Firefox下用javascript创建的表单元素无法正确提交的bug"
date: 2013-12-01 12:53
comments: true
categories: ["bugs", "javascript"]
---
项目[StackTXT](https://github.com/xd547/stacktxt)
描述：   
Firefox下 document.createElement 创建的表单元素无法正确提交(submit)   

解决：
将创建的表单元素加入到 document.body 提交完毕后删除
<!-- more -->
```coffee-script 
share = ->
  shareForm = document.createElement("form")
  shareForm.setAttribute("method", "post")
  shareForm.setAttribute("action", "/")

  shareForm.appendChild(share_content)
  # add shareForm to DOM
  document.body.appendChild(shareForm)
  shareForm.submit()
  # remove shaerForm from DOM
  document.body.removeChild(shareForm)
  return
```


