---
layout: post
title: "Rails Notes: Make Dropdown Work on Heroku"
date: 2014-10-26 12:07:57 +0800
comments: true
categories: Rails
keywords: rails, heroku, bootstrap, dropdown
description: 
---

After I deployed a app on Heroku, I found that the Bootstap dropdown menu didn't work on Heroku. It was working in my local development environment.

So I searched the answers from stackoverflow. It seemed that it was a common problem. 

I had tried to reorder the //=jquery and //=bootstrap and reorder back again and again. I also ran asset precompile method. However, it still didn't work in my situation.

Then I compared an app I wrote before, and I found that there might be the conflict between 'bootstrap' and 'bootstrap-spockets'. The dropdown menu worked if either of them were removed. So I removed 'bootstrap' finally. I am not sure if it will cause any further problems but so far it's fine.  


