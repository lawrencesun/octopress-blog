---
layout: post
title: "Rails Notes: Adding a 'Like' Function"
date: 2014-02-11 21:16:52 +0800
comments: true
categories: [Rails, Ruby]

keywords: ruby, rails, polymorphic, likeable, ajax
description: Adding a 'Like' function to posts and comments in rails, using polymorphic association, as well as ajax.
---
Instead of text comments, we can easily state our opinion on the article by clicking a 'Like' button or a 'Vote' arrow. It is not uncommon that this function plays a role of recommendation. Last week, I added a similar function called 'Likeable' to my blog-like app. Here is the key notes. 

<!-- more -->
__Migration and Models__

First of all, we `rails g migration create_likes`.

```ruby
class CreateLikes < ActiveRecord::Migration
  def up
    create_table :likes do |t|
    	t.boolean :like
    	t.references :likeable, polymorphic: true
    	t.integer :user_id
    	t.timestamps
    end
  end

  def down
  	drop_table :likes
  end
end
```

Here, like boolean will show the choice of like(true) or unlike(false), :user_id will show who made the choice. 

`t.references :likeable, polymorphic: true` will generate two columns: _likeable_id_ and _likeable_type_ (e.g. Post or Comment). 

With _Polymorphic_ association, a model can belong to more than one other model, on a single association [[1]][1]. In our case, like model can belong to post model or comment model. 

```ruby
class Like < ActiveRecord::Base
	belongs_to :user
	belongs_to :likeable, polymorphic: true.
end

class Post < ActiveRecord::Base
	has_many :likes, as :likeable
end

class Comment < ActiveRecord::Base
	has_many :likes, as :likeable
end
```

__Routes__

We would like to use a member routes here.
```ruby
resources :posts do
  	member do 
  		post 'like'
  	end
    resources :comments do
    	member do
    		post 'like'
    	end
    end
end
```

It will recognise '/post/post_id/like' and '/post/post_id/comment/id/like' with POST, route to the action of PostsController and CommentsController, create the like_post_path and like_post_comment_path helpers.

__Views__

'app/views/posts/show.html.erb'
```ruby
<span class="pull-right">
	<%= link_to like_post_path(@post, like: true), method: 'post' do %>
  <i class="icon-thumbs-up"></i>
	<%= @post.likes.size %>likes %>
	<% end %>
</span>
```
Here we specify the method POST instead of the default a_tag method GET, by adding `method: 'post'`. This will add 'data_method="post"' to link tag, which will revoke a javascript in rails that convert the link tag to a form and submit it. We also pass in a params 'like' to be true, which means it will count as a 'Like'.

Now we need some actions.

__Action__

```ruby
class PostsController < ApplicationController
	before_action :find_post, only:[…, :like]
	before_action :require_user, except:[…]
	…

	def like
		Like.create(likeable: @post, user: current_user, like: params[:like])
		flash[:success] = "Like Counted!"
		redirect_to :back
	end
```

Here, we will pass params of @post to likeable_id and likeable_type, current_user 's id and set 'true' on like boolean. 

__Ajax__

Every time we hit the 'Thumbs-up' button, it will hit the datebase and rebuild the whole page. It's quite expensive. So we will use another rails magic here.

Simply add `remote: true`: '<%= link_to like_post_path(@post, like: true), method: 'post', remote: true do %>'. It will become a ajax request when we hit the 'Thumbs-up'.

In the meantime, we add response to this request in controller, revise our show.html.erb and add a `like.js.erb` view template file in app/views/posts, respectively.

```ruby
def like
		...
	respond_to do |format|
		format.html do
		flash[:success] = "Like Counted!"
		redirect_to :back
	end
	
	format.js
	end			
end
```  

```ruby
<span id="post_<%= @post.id %>_likes"><%= @post.likes.size %>likes</span>
```

```ruby
$("#post_<%= @post.id %>_likes").html("<%= @post.likes.size %>likes"); 
```

__Expansion__

We can also add a 'Unlike' function with a 'Thumbs-down' button.
Instead of `like: true` params, we set it to false. In order to show the total likes, we add a method called `total_likes` in post.rb.

```ruby
def total_likes
	self.likes.where(like: true).size - self.likes.where(like :false).size
end
```

[1]: http://guides.rubyonrails.org/association_basics.html#polymorphic-associations


  
