---
layout: post
title: "Rails Notes: How to 'Like' once and 'Unlike' in Rails"
date: 2014-08-03 21:35:00 +0800
comments: true
categories: [Rails, Ruby]

keywords: ruby, rails, likeable, jquery
description: How to limited users to "Like" once and "Unlike" in Ruby on Rails.
---
Few months ago, I added a ['Like' function](/posts/2014/02/11/rails-notes-adding-a-like-function/) to my rails project. It's a lovely function, but has some problems left. One is that users could like a single post/comment as much as they can, the other one is that users are not able to unlike.

Thus, I tried to improve the likeable function this weekend. First thing I came up with my head was to add a uniqueness validation. 

<!-- more -->

Here is the code I added in `like.rb`.

```ruby
validates_uniqueness_of :user_id, scope: [:likeable_id, :likeable_type]
```

It worked. 'User' could not cheat any more, well, up to a point. But if users want to their mind later on, they are not allowed to do so.

Regarding the second issue, I decided to handle it this way.

Let's assume there is a user called Mr.R. R liked a post, the params[:like] set to true. We would like to provide a link so that R could send another POST with the params[:like] = false. If R haven't make the choice or unliked it before, then a link to POST with the params[:like] = true is available.

First of all, I changed the like method in `posts_controller`.

```ruby
def like
	if post_liked 
		@like.update(like: params[:like])
	else 
		Like.create(likeable: @post, user: current_user, like: params[:like])
	end
	respond_to do |format|
		format.html do
			flash[:success] = "Like Updated!"
			redirect_to :back
		end
		format.js
	end	
end

private

def post_liked
	liked?(@post)
end
```

Here, if R has already liked/unliked the post, and then changed his mind, we would update the `like` attribute rather than creating another data.

There are two methods in `application_controller`, which are available to views and posts/comments controllers.

```ruby
def liked?(type)
  @like = Like.find_by(likeable: type, user: current_user) 
end

def liked(type)
  @like = Like.find_by(likeable: type, user: current_user)
  if @like
    return @like.like
  else
    return false
  end
end
```

Then I adjusted `show.html.erb`in the view. As discussed before, I provided the oppsite like/unlike button to users' former dicision.

```ruby
<span id= "post_like" class="pull-right">
 	<% if signed_in? && liked(@post) %>
 		<%= link_to like_post_path(@post, like: false), method: 'post', remote: true do %>
 			<span id="user_like_<%= @post.id %>" class="like"><i class="fa fa-thumbs-o-up"></i></span>
 		<% end %>	
 	<% else %>				
 		<%= link_to like_post_path(@post, like: true), method: 'post', remote: true do %>
			<span id="user_like_<%= @post.id %>"><i class="fa fa-thumbs-o-up"></i></span>
 		<% end %>
 	<% end %>
 ...
 </span>
``` 

Finally, I changed the `like.js.erb` file in order to present the difference between like and unlike, as well as the user's new dicision.

```ruby
<% if liked(@post)%>
 	$("#user_like_<%= @post.id %>").addClass("like");
<% else %>
 	$("#user_like_<%= @post.id %>").removeClass("like");
<% end %>

$("#post_like").load("<%= @post.id %> #post_like")
```

The comments like/unlike function is similar.

This improvement killed me a lot time this weekend, but thankfully, it worked at last. However, there must be a much easier & clean way to do it.

By the way, I had updated my app to bootstrap 3.2, a more resposive-friendly version. It was an nightmare to change the style, like css class/id name etc. I also used fontawesome icons instead of the old ones. The app looks just fine.
