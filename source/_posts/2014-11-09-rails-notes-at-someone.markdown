---
layout: post
title: "Rails Notes: At or Reply to Someone"
date: 2014-11-09 08:52:19 +0800
comments: true
categories: Rails
keywords: rails, atwho, comment
description: 
---
__1. At__

The '@' function is used widely on web apps. 

Here I use a jquery plugin called [atwho][1]. First download jquery.atwho.js, jquery.caret.js, jquery.atwho.css. Then add these files to app/assets.

Add `//= require jquery.atwho` to app/assets/javascripts/application.js and add `*= require jquery.atwho` to app/assets/stylesheets/application.css.scss.

<!-- more -->

Then use it to at users when replying a comment. Following [happycasts][2], bind the textarea like this:

```
$(function(){
  var commenter = [];
	var commenter_exist= [];
	$('.comment-user').each(function() {
  	if($.inArray($(this).text(), commenter_exist) < 0){
      commenter.push($(this).text());
      commenter_exist.push($(this).text());
    }
	});
  $('textarea').atwho({at:"@", 'data':commenter});
});
```

One problem I found was this atwho funciont didn't work until refreshed this page. When I deleted turoblink, it worked fine. So Finally I solved it by using a gem called [jquery.turbolinks][3].

__2. Reply__

Sometimes we would like to reply to other's comment. When we click reply button, we want to '@' this comment's author.

Thanks to [Campo][4] app, I added reply function to my own rails app.

Add following parts to comments view. 

```
<a class="btn", data-reply-to="@<%= comment.user.name %> " >
	<i class="fa fa-reply"></i>
</a>
```

Then include a coffee javascript file. Remember to set corrosponding class/id in the html file.

```
$(document).on 'click', '.comments [data-reply-to]', ->
  textarea = $('#new_comment textarea')
  textarea.focus()
  textarea.val(textarea.val() + $(this).data('reply-to'))
```



[1]: https://github.com/ichord/At.js
[2]: http://happycasts.net/episodes/76
[3]: https://github.com/kossnocorp/jquery.turbolinks
[4]: https://github.com/chloerei/campo