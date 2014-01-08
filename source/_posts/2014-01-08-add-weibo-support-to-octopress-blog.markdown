---
layout: post
title: "Add Weibo Support to Octopress Blog"
date: 2014-01-08 19:39:38 +0000
comments: true
categories: Octopress

keywords: octopress, sina weibo
description: Add Sina Weibo link to Octopress Blog.
---

Since I will go back China very soon, I think adding a _Weibo_ link on the sidebar might be a good move. To be honest, I am not too keen on Weibo. The reason? Information overload. 


* First, add following code to "/source/_includes/header.html" 

``` 
	{ % if site.weibo_user % }
		<a class="weibo" href="http://www.weibo.com/{{ site.weibo_user }}" title="Weibo">Weibo</a>
	{ % endif % }
```
* Second, add following code to "/sass/parts/_header.scss"

``` css
&.weibo{
  background: image-url('social/weibo.png') center no-repeat #e32529;
  border: 1px solid #e32529;
  &:hover{
      border: 1px solid darken(#e32529, 10%);
  }
}
```
* Then, add the [“weibo.png”](https://github.com/lawrencesun/lawrencesun.github.io/tree/source/source/images/social) to “/source/images/social” dirctory.
* Finally, add following code to "_config.yml"

```
# Weibo
weibo_user: ylsun # 微博数字 ID 或域名 ID
weibo_share: true 
```
<sub>Thanks for sharing [Allen](http://www.imallen.com/blog/2013/05/12/add-support-for-weibo-and-dribbble-to-greyshade.html) </sub>

