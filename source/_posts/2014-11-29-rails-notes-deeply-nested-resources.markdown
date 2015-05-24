---
layout: post
title: "Rails Notes: Deeply Nested Resources"
date: 2014-11-29 21:29:26 +0800
comments: true
categories: Rails
keywords: rails, polymorphic resources, nested resources, RESTful 
description: 
---
Nested resources are commonly used in rails app. For example,

```
resources :books do
  resources :posts do
   	resources :comments
  end
  resources :comments
end
```

However, when it's deeply-nested, the URL will looks like this as a monster, `/books/:id/posts/:id/comments`. And it will cause some difficulties when trying to create/edit comments. 

<!-- more -->

For example, `book_post_comment_path(@book, @post, @comment)` looks complicated and non reusable.

It's also hard to handle different params in comments controller.

So I tried to refactor the code today.

First I changed the routes.rb:

```ruby
resources :books do
  resources :posts
  resources :comments
end

resources :posts do 
  resources :comments
end
```

This will generate the routes like this:

```ruby
books/:id/posts
books/:id/comments
posts/:id
posts/:id/comments
```

In this way, the comments action can be used in the same way whatever the @commentable is. 

The posts controller will not need @postable id except new/create action. If you want to create a new post on books show page like me, you still have to pass books_id to @postable. 

And for me, after creating the new post, it will redirect_to @post rather than [@postable, @post]. So when creating new comment for post, it happens on posts/:id page. I also added a link back to this post's parent using @post.postable .

