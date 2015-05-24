---
layout: post
title: "Rails Notes: Link to Commentable"
date: 2014-11-08 17:19:09 +0800
comments: true
categories: Rails
keywords: rails, polymorphic, commentable
description: 
---
Few days ago I created a polymorphic comment model based on [Railscasts](http://railscasts.com/episodes/154-polymorphic-association).

Today I wanted to show the lastest comments on homepage and link each comment body to their 'parents' like a book or a movie.

The link path was a bit tricky and finally I figured it out.

What you need here is this particular comment's commentable type like 'book' or 'movie', as well as the commentable id, so it will leads you to the show page and the url should like 'book/id'.

<!-- more -->

The view page could look like this.

```ruby
<%= link_to markdown(@comment.body), [@comment.commentable, @comment] %>
``` 

Then it will pass commentable type and id, looking for comment/show action.

So add show action in comments_controller like this

```ruby
def show
	redirect_to @commentable
end
```
This will direct to the page that I want.

