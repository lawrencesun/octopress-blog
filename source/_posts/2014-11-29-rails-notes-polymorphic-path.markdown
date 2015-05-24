---
layout: post
title: "Rails Notes: Polymorphic Path"
date: 2014-11-29 21:26:30 +0800
comments: true
categories: Rails
keywords: rails, polymorphic path
description: 
---
In my rails app, movie/book and post are in the _polymorphic_ relationship. In `posts/show.html.erb`, I tried to add 'Edit' and 'Delete' function. The problem was how to write related path.

At first, I used _if...else_ condition to judge @postable_type. When it equals to 'Book', then showed `edit_book_post_path(@post.postable_id, @post)` and `book_path(@post.postable_id)`, and if it equals to 'Movie', then showed the related movie path.

It worked fine, but a little bit complicated. The same problem when I render the comments partial, I had to use two different files to handle edit/delete comment path.

Then I found polymorphic_path method on RailsGuides and some articles on the Web.

<!-- more -->

Briefly speaking, the final code I use like this:

```ruby
link_to 'Edit', edit_polymorphic_path([@postable, @post])
link_to 'Delete', polymorphic_path([@postable, @post])
link_to 'New Post', new_polymorphic_path([@postable, @post])
```

Now, I can abandon the if...else condition and use the same template whatever the @postable is.

I also refactor the controller like this:

```ruby
def create
	...
	if @post.save
		...
	redirect_to [@postable, @post]
		...
end
```

Doing this way, it's simple and clear.
