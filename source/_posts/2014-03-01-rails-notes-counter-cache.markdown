---
layout: post
title: "Rails Notes: Counter cache"
date: 2014-03-01 10:18:40 +0800
comments: true
categories: [Rails, Ruby]
keywords: rails, counter cache, scope, active record query
description: sort posts by the number of comments using counter cache and scope. 
---
By default, posts in my app are sorted by create_at time. I would like to sort them by the number of comments. In order to implement it, we will apply :counter_cache and :scope.

<!-- more -->

According to Active Record Association on Rails Guides, @post.comments.size requires making a call to the database to perform a COUNT(*) query. :counter_cache can be used to make finding the number of belonging objects more efficient.

First, run a migration to add the column called comments_count to posts.

```ruby
class AddCommentsCountToPosts < ActiveRecord::Migration
 	def self.up
    add_column :posts, :comments_count, :integer, :null => false, :default => 0

    Post.reset_column_information
    Post.all.each do |p|
      p.update_attribute :comments_count, p.comments.length
    end
  end

  def self.down
    remove_column :posts, :comments_count
  end
end
```
Then add following code to `comment.rb` 

```ruby
belongs_to :post, counter_cache: true
```

Then we will use scopes. According to Active Record Querying, scoping allows you to specify commonly-used queries which can be referenced as method calls on the association objects or models.

In our case, define a scope called active on `post.rb`

```ruby
scope :active, -> { where("comments_count >= ?", 5) }
```

Then add routes to the `collection`.

```ruby
resources :posts do 
	...
	collection do 
		get 'active'
	end
	...
end
```

it will generate the path /posts/active with GET, route to the active action of PostsController, as well as active_posts_path route helper.

In PostsController, define `active` action

```ruby
def active
		@posts = Post.active.order('created_at DESC').page(params[:page])
		render action: 'index'
end
```

Finally, use @post.comments_count and active_posts_path in views to achieve what we want at the beginning.

