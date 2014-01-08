---
layout: post
title: "Add About Page"
date: 2014-01-08 20:47:46 +0000
comments: true
categories: Octopress

keywords: octopress, about page
description: Add About Page
---
Three steps:

* Run 
 ```
 rake new_page[about]
 ```
* Add following code to "/source/_includes/custom/navigation.html file"
 
```
 <li><a href="{{ root_url }}/about">About</a></li>
```

* Edit "/about/index.markdown" file if you want.