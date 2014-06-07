---
layout: post
title: "Rails Notes: Ajaxify Pagination"
date: 2014-02-22 11:25:16 +0800
comments: true
categories: [Rails, Ruby]
keywords: rails, pagination, kaminari, pagination ajax, kaminari ajax pagination
description: how to ajaxify Kaminari pagination 
---
After applying [pagination][1] in my app, I would like to ajaxify kaminari paginator. I googled and stackoverflowed this issue, and found the solution, which is quite similar to what I did in [likeable][2] function. 

<!-- more -->

First, add `remote: true` and class id to `index.html.erb`.

```ruby
<div id="posts">
	<%= render 'shared/posts', object: @posts %>
</div>
<div id="paginator">
 	<%= paginate @posts, remote: true %>
</div>
```

Second, create a new file `index.js.erb`.

```ruby
$("#posts").html("<%= escape_javascript render 'shared/posts', object: @posts %>");
$("#paginator").html("<%= escape_javascript(paginate(@posts, remote: true).to_s) %>");
```
Done.

[1]: /posts/2014/02/16/rails-notes-pagination-with-kaminari/
[2]: /posts/2014/02/11/rails-notes-adding-a-like-function/
