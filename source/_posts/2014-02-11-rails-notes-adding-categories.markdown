---
layout: post
title: "Rails Notes: Adding categories"
date: 2014-02-11 20:23:51 +0800
comments: true
categories: [Rails, Ruby]

keywords: ruby, rails, m-m relationship
description: add categories to post in a rails app, a m-m relationship.
---
In the third note, a brief description about adding categories in a blog-like app will be presented. It is a [M-M relationship](http://guides.rubyonrails.org/association_basics.html). We will  apply `has_many :through` association. 

<!-- more -->

__Migration__:

```ruby
class CreatePostCategories < ActiveRecord::Migration
  def up
    create_table :post_categories do |t|
    	t.integer :post_id, :category_id
    	
    	t.timestamps
    end
  end

  def down
  	drop_table :post_categories
  end
end
```
PostCategories will have two foreign keys: :post_id and :category_id

__Models__:

```ruby
class PostCategory < ActiveRecord::Base
	belongs_to :post
	belongs_to :category
end
```

```ruby
class Category < ActiveRecord::Base
	has_many :post_categories
	has_many :posts, through: :post_categories
	...
end
```

```ruby
class Post < ActiveRecord::Base
	...
	has_many :post_categories
	has_many :categories, through: :post_categories
	...
end
```

__Controller and routes__

similar to other resources.

__Views__

'shared/_posts.html.erb'
```ruby
<% object.reverse.each do |post| %>
	<p>
		<span class="badge pull-right"><%= post.comments.count %></span>
		<%= link_to "#{post.title}", post_path(post) %><br>
		<% post.categories.each do |category| %>
	    <%= link_to category.name, category_path(category), class: "label" %>
	  <% end %>
	  <span class="timestamp">
			<small>posted by <% unless post.user.blank? %>
				<%= link_to post.user.username, user_path(post.user) %>
				<% end %>
				<%= time_ago_in_words(post.created_at)%> ago 
			</small>
		</span>
		<hr>
	</p>
<% end %>
```

'app/views/categories/show.html.erb'
```ruby
...
<%= render 'shared/posts', object: @category.posts %>
...
```

Add categories selection in _form partial
```ruby
<div class='control-group'>
  <%= f.label "选择节点" %>
  <%= f.select :category_ids, Category.all.collect{|c| [c.name, c.id]}, {}, {multiple: false} %>
</div>
```

Add get_categories method in ApplicationController to show categories in header.
```ruby
def get_categories
	@categories = Category.all
end

before_action :get_categories

<% @categories.each do |cat| %>
<li><%= link_to cat.name, category_path(cat) %></li>	
<% end %> 
```
 