---
layout: post
title: "Rails Notes: Add Administrator"
date: 2014-02-15 16:02:33 +0800
comments: true
categories: [Rails, Ruby]

keywords: rails, administrator
description: add administrator to a rails app.
---
On the land of website/software, there is always an Administrator. The Administrator, like a king, has the right to change the rules, block the news and 'kill' the innocent users whenever he likes.

> Dieu et mon droit. 

Today, I claimed to the throne -- became an admin who had the power to delete comments and users. 

<!-- more -->

First, `rails g migration add_admin_to_users`. 

Second, create a table and run `rake db:migrate`

```ruby
class AddAdminToUsers < ActiveRecord::Migration
  def change
  	add_column :users, :admin, :boolean, default: false
  end
end 
```

Then, define a method in ApplicationController.

```ruby
def admin_user
  unless current_user.admin?   # line_to_be_modified 
    flash[:error] = "Please contact admin."
    redirect_to root_path
  end
end
```

Next, add `destroy` to UsersController

```ruby
...
before_action :admin_user, only: :destroy
...
def destroy
	@user.destroy
	flash[:success] = "User deleted."
	redirect_to users_path
end 
```

Afterwards, add a delete link in the view.

```ruby
<%= link_to 'delete', user_path(user), method: 'delete', data: { confirm: 'You sure?'} %>
```

Finally, we would like to destroy the posts and comments automatically at the same time we delete the users. `has_many :posts, dependent: :destroy`

*******

* Issue 1:
There would be a nil class error if there was no user signed in, `current_user.admin?` went wrong. Therefore, I added another method in ApplicationController and change the line_to_be_modified to `unless admin?`

```ruby
def admin?
	signed_in? && current_user.admin?
end
```

* Issue 2:
Another error occurred when I tried to add the delete link on the comments partial. 

```ruby
<% if object.comments.any? %>
	<% object.comments.each do |comment| %>
	...
	<% if admin? %>
		<%= link_to 'delete', post_comment_path($params), method: 'delete', data: { confirm: 'You sure?'} %>
	<% end %>
	...
```

At first, I passed in the 'comment' as $params, and it showed that  the post.id and :id were nil. So I changed the $params to 'comment.post.id, comment.id' and it went well.