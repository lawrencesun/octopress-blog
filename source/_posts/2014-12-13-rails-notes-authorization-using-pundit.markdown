---
layout: post
title: "Rails Notes: Authorization using Pundit"
date: 2014-12-13 09:39:39 +0800
comments: true
categories: Rails
keywords: rails, authorization, pundit
description: 
---
Authentitcation identifies a user, and authorization controls what a user can do. The most famous gem used for authorization in rails is [CanCan][1], which is developed by Ryan Bates. However, this gem was no longer maintained. [CanCanCan][2] and [Pundit][3] are alternative gems we could use. Here I choosed Pundit.

First, implement user roles. Define roles in User Model by using enum and add a new column to user table.

<!-- more -->

`enum role: [:guest, :vip, :admin]`

`rails g migration AddRoleToUsers role:integer`

Set the default user as guest. We could assign roles like user1.admin! and question the roles like user1.admin?

```ruby
class AddRoleToUsers < ActiveRecord::Migration
  def self.up
  	add_column :users, :role, :integer, default: 0
  end

  def self.down
  	remove_column :users, :role
  end
end
```

Next, `gem "pundit"` into Gemfile and run bundle install.

Run `rails g pundit:install` to generate a default policy in app/policies/application_policy.rb

Then create own policy, for example, a Post Policy.

```ruby
class PostPolicy < ApplicationPolicy
	attr_reader :current_user, :post
  def initialize(current_user, post)
    @current_user = current_user
    @post = post
  end

  def update?
    @current_user.admin? or @post.user == @current_user
  end

  def destroy?
  	@current_user.admin? or @post.user == @current_user
  end
end
```

So if current user is not the author of this post or admin, then he is not allowed to update or destroy the post.

After that, add `autorize @post` to update and destroy action in Post Controller.

In Post show page, if current user do not have the permission, he will not see the edit and delete button.

```ruby
<% if policy(@post).update? %>
	...
<% end %>
```

Finally, add following code to Application Controller, so that we include Pundit and show the customized alert.

```ruby
...
include Pundit
...
rescue_from Pundit::NotAuthorizedError, with: :user_not_authorized

  private

  def user_not_authorized
    redirect_to :back, :alert => "You don't have permission to those resources."
  end
```

UPDATE:

Sometimes current_user are not defined, for example a guest user who is not signed in. Then the above authorization may raise a `undefined method 'admin?' for nil:NilClass` problem, which I met. The solution would be changing `current_user.admin?` to `current_user.try(:admin?)`.









[1]: railscasts.com/episodes/192-authorization-with-cancan
[2]: https://github.com/CanCanCommunity/cancancan
[3]: https://github.com/elabs/pundit