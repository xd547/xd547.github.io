---
layout: post
title: "Ruby on Rails Rspec 测试的上传模拟"
date: 2013-04-12 10:13
comments: true
categories: ["编程", "测试"]
---
Rspec 是一个 ruby 的测试框架，在进行 Ruby on Rails 开发时通常都会使用这个框架  
本文不讨论如何在 Ruby on Rails 中 配置 Rspec  
0.准备工作  
  
0).在 Ruby on Rails 项目文件夹下的 spec/fixtures 下创建文件夹 files  
1).把测试文件拷贝到该文件夹下面，这里的示例为 "egg.zip", "egg.jpg"  
  
1.引入 fixture_file_upload   
```ruby
include ActionDispatch::TestProcess
```
  
2.为测试准备上传文件  
```ruby
describe UploadController do
  before(:each) do
    @file = fixture_file_upload('/files/egg.zip', 'application/zip')
    @image = fixture_file_upload('/files/egg.jpg', 'image/jpeg')
  end
end
```
3.在测试中上传文件  
```ruby
describe "upload" do
  it "should upload file" do
    post :upload, :file => @file  #upload action is here
    expect(response).to redirect_to(upload_path)
    flash[:notice].include?("upload success").should equal(true)
  end

  it "should upload image" do
    post :upload, :file => @image  #upload action is here
    expect(response).to redirect_to(upload_path)
    flash[:notice].include?("upload success").should equal(true)
  end
end
```
4.运行测试  
```bash
rspec spec/controllers/upload_controller_spec.rb
```
5.完整的 spec  
```ruby upload_controller_spec.rb
require 'spec_helper'
include ActionDispatch::TestProcess

describe UploadController do
  before(:each) do
    @file = fixture_file_upload('/files/egg.zip', 'application/zip')
    @image = fixture_file_upload('/files/egg.jpg', 'image/jpeg')
  end

  describe "upload" do
    it "should upload file" do
      post :upload, :file => @file  #upload action is here
      expect(response).to redirect_to(upload_path)
      flash[:notice].include?("upload success").should equal(true)
    end

    it "should upload image" do
      post :upload, :file => @image  #upload action is here
      expect(response).to redirect_to(upload_path)
      flash[:notice].include?("upload success").should equal(true)
    end
  end
end
```

