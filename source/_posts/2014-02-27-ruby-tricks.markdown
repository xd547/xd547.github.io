---
layout: post
title: "Ruby 小技巧"
date: 2014-02-27 22:54
comments: true
categories: ["ruby"]
---

Iterrator 有时需要index，那就加入with_index，如下
```ruby
f = File.open("testfile")
f.each.with_index do |line, index|
  puts "Line #{index} is: #{line}"
end
```

在Rails的migration中添加字段（add_column）option的limit说设置的值
是添加字段类型的默认值，limit就不会出现在db/schema.rb中的相应位置。
