---
layout: post
title: "使用through关联时访问关系的属性"
date: 2013-12-26 21:49
comments: true
categories: ["rails"]
---
0.有下面三个 model 关联，使用 through 来简化对 Ownership 的访问    
```ruby
class User < ActiveRecord::Base
  # association
  has_many :ownerships, dependent: :destroy
  has_many :shops, through: :ownerships
end

class Ownership < ActiveRecord::Base
  belongs_to :user
  belongs_to :shop
end

class Shop < ActiveRecord::Base
  # association
  has_one  :ownership, dependent: :destroy
  has_one  :user, through: :ownership
end
```
1.此时无法在Shop中在 after_create 来访问 ownership 的 user_id 属性，因为这时 owership 还未创建。当事务 commit 以后 owership 被创建，同时拥有 user_id 属性。这时就可以获得 user_id 从而进行相关操作    
```ruby
class Shop < ActiveRecord::Base
  after_commit :get_attr_after_destroy, on: [:destroy]
  after_commit :get_attr_after_create,  on: [:create]
  
  after_create :get_attr_in_after_create

  # association
  has_one  :ownership, dependent: :destroy
  has_one  :user, through: :ownership

  private
    def get_attr_after_destroy
      user_id = self.ownership.user_id
    end

    def get_attr_after_create
      user_id = self.ownership.user_id
    end

    def get_attr_in_after_create
      # self.ownership is nil
      # NoMethodError: undefined method `user_id' for nil:NilClass
      user_id = self.ownership.user_id
    end
end
```