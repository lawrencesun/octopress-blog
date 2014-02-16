---
layout: post
title: "Rails Notes: Pagination with Kaminari"
date: 2014-02-16 22:13:47 +0800
comments: true
categories: [Rails, Ruby]

keywords: rails, pagination, kaminari
description: add pagination to a rails app, using Kaminari.
---
With hundreds and thousands of posts on website, we would like to insert some breaks. The first choice for pagination in Rails is will_paginate. However, I find a better one called [Kaminari](https://github.com/amatsuda/kaminari).  

<!-- more -->
__Controllers and Views__

First, add 'kaminari' to 'Gemfile' and run `bundle install`

Second, we apply the scope called `page` and `per` in our Controller.

`@posts = Post.all.page(params[:page]).per(15)`

that will show 15 posts per each page.

Then, we call `paginate` helper in our views.

`<%= paginate @posts %>`

__Configure the default value__

run `rails g kaminari:config` and change the file `kaminari_config.rb` in config/initializers

```ruby
Kaminari.configure do |config|
  config.default_per_page = 15
  # config.max_per_page = nil
  config.window = 2
  config.outer_window = 1
  # config.left = 0
  # config.right = 0
  # config.page_method_name = :page
  # config.param_name = :page
end
```

__I18N and labels__

We can change the default labels in config/locales.

```ruby
en:
  views:
    pagination:
      first: "&laquo; First"
      last: "Last &raquo;"
      previous: "&lsaquo; Prev"
      next: "Next &rsaquo;"
      truncate: "..."
```

__Configure the default view__

run `rails g kaminari:views default` and change the files in app/views/kaminari

e.g. I delete the first and last label in _pagination.html.erb and change the style to bootstrap according to the theme provided by the Kaminari.

__Issue__

I followed the instruction on Railscasts and the wiki of Kaminari. But my case seemed more complicated. I set three posts per page as default for test, but found that the posts presented in a strange array. 

PostsController:
```ruby
def index
	...
	@posts = Post.all.page(params[:page])
	...
end
``` 

index.html.erb:
```ruby
<%= render 'shared/posts' , object: @posts%>
<%= paginate @posts %>
```

_posts.html.erb:
```ruby
<% object.reverse.each do |post| %>
```
 
The problem was that 3 posts each time was passed into the partial and was implemented the reverse scope. So I deleted the `reverse` scope and changed the line in the controller like this:

```ruby
@posts = @posts.order('created_at DESC').page(params[:page])
```




