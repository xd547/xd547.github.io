---
layout: post
title: "Ruby on Rails 中灵活的应用程序设置（二）"
date: 2013-04-29 16:03
comments: true
categories: "编程"
---
在上一篇[Ruby on Rails 中灵活的应用程序设置（一）](/blog/2013/04/15/flexible-setting-in-rails-app-1/)中讲到如何创建一个 AppSettings 的 model 这篇要讲的是在 controller 和 view 中使用 AppSetting  
0.创建 AppSetting Controller  
```bash
rails g controller AppSetting index
```
1.新建一个 form partials    
<!-- more -->
```html _form.html.erb
<!-- 实际运用时里面的中文可以用 I18n处理 -->
<table>
    <tr>
      <th>属性</th>
      <th>值</th>
    </tr>
  <%= form_tag(app_setting_path) do -%>
    <tr>
      <td>
        <%= label_tag :site_name, "站点名称"%>
      </td>
      <td>
        <%= text_field_tag :site_name, AppSetting["site_name"] %>
      </td>
    </tr>

    <tr>
      <td>
        <%= label_tag :site_url, "站点链接" %>
      </td>
      <td>
        <%= text_field_tag :site_url, AppSetting["site_url"] %>
      </td>
    </tr>
    
    <tr>
      <td>
      </td>
      <td>
        <%= submit_tag "保存" %>
      </td>
    </tr>
  <% end %>
</table>
```

2.下面直接给出 AppSettingController 文件  
```ruby app_setting_controller.rb
class AppSettingController < ApplicationController
  def index
    method = request.method

    # 处理 POST
    if method == "POST"
      # 验证字段 具体看下面的 validate_setting_length 方法
      validation = validate_setting_length(params)
      if validation.length == 0
        # 如果验证通过 保存每一个字段
        setting_fields.each do |v|
          AppSetting[v] = params[v]
        end
        msg = I18n.t("app_setting.updated_msg")
        flash[:notice] = msg
      else
        msg = I18n.t("app_setting.error_title", :error_count => validation.length)
        validation.each do |value|
          msg += I18n.t("app_setting.error_msg", 
            :field => I18n.t("app_setting.#{value}"))
        end
        flash[:error] = msg
      end
      respond_to do |format|
        format.html { redirect_to app_setting_url }
      end
    end
  end

  private
    # 验证字段 这里只验证每个字段的最大长度，需要其他的自己添加
    def validate_setting_length(params)
      max_length = 255
      fields = []
      setting_fields.each do |v|
        if params[v].length > max_length
          fields.push(v)
        end
      end
      fields
    end

    def setting_fields
      # 把需要保存的设置项放到这个 array 里面
      ["site_name", "site_url"] 
    end
end

```
分析：在 AppSettings 保存时 每设置一个字段都会保存一次，可以添加所有字段一次处理完以后再保存的代码， 而 AppSetting 在读取时，第一次是往数据库中读取，再次读取时则从 cache（由 rails 控制） 里面读取，所以性能尚可。   