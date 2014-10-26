---
layout: post
title: "Rails Notes: Heroku environment configuration problem"
date: 2014-10-26 12:06:49 +0800
comments: true
categories: Rails
keywords: rails, heroku, secret.yml, devise
description: 
---

When I tried to deploy one of my rails app on Heroku, the following problem occured.

```
can't convert nil into String 
/app/config/initializers/devise.rb:13:in `+'
```
The reason was that I set DOMAIN_NAME etc. inside secret.yml and this file was git ignored since I wanted to keep credentials private. So Heroku couldn't find these configuration values.

Using figaro gem seems a good way to fix this. But I didn't want to add another gem and create a new yml file.

What I did was set Heroku environment variables directly and it worked:

```
$ heroku config:add GMAIL_USERNAME='myname@gmail.com' GMAIL_PASSWORD='mypassword'
$ heroku config:add DOMAIN_NAME='example.com'
```