---
layout: post
title: "Blogging with Octopress -- Posts, Comments and SEO"
date: 2013-12-21 17:34:23 +0000
comments: true
categories: Octopress

keywords: markdown, disqus, SEO, octopress
description: How to blogging with Octopress, use markdown and disqus, better SEO.
---

Run the following code to generate a new post:

``` ruby
rake new_post["new post"]
```
Octopress uses [Markdown](http://daringfireball.net/projects/markdown/), which's clean and simple as writing a text. I am still learning how to use it well.

In order to get better SEO optimizations, add _keywords_ and _description_ at the beginning of each post after _catagories_.

Octopress also allows us to add google analytics. Simply add Analytics ID in _config.yml file and add included

``` 
{% include google_analytics.html %} 
```
in _includes/custom/after_footer.html. 

If you want to add comments after each post like me, add Disqus shortname in _config.yml file and set URL, username etc. in Disqus Setting.   