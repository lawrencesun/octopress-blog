---
layout: post
title: "Hello Octopress"
date: 2013-12-19 02:13:05 +0000
comments: true
categories: Octopress

keywords: octopress, wordpress, markdown, git
description: How to set up Octopress Blog and the advantages of Octopress blog.
---

I have a blog built by powerful Wordpress and quite like it. There are plenty of great plugins and themes to help you customize blog. 

[__Octopress__](http://octopress.org) is definately built for hackers. Pure words and command lines. So I think building a Octopress blog would be a good way to learn and share.

The _advantages_ of Octopress:

*  static page
*  markdown
*  hosting on github pages
  
Setup Octopress is easy if you are familar with ruby and git.
```  ruby 
git clone git://github.com/imathis/octopress.git octopress
cd octopress
```

Then install dependencies and default theme.
```  ruby 
gem install bundler
bundler install
rake install
```

Using third-party theme
```  ruby 
git clone git@github.com:USERNAME/THEMENAME.git .themes/THEMENAME
rake "install[THEMENAME]"
```
