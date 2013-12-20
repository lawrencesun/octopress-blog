---
layout: post
title: "Deploying Octopress to Github Pages and Custom Domain"
date: 2013-12-20 00:16:04 +0000
comments: true
categories: Octopress
---

After [_Set up Octopress blog_](/blog/2013/12/19/set-up-octopress), I tried to deploying it to my own _subdomain_. 

First, create a new Github repository called "username.github.io". e.g. I created a new repo "lawrencesun.github.io".

Then run:

``` ruby
rake setup_github_pages
```

Copy SSH URL of this new repo(e.g. git@github.com:lawrencesun/lawrencesun.github.io.git)

Next run:

``` ruby
rake generate
rake deploy
```

Run _rake preview_ to see the blog at _localhost:4000_ 

Commit to source branch:

``` ruby
git add .
git commit -m "message"
git push origin source
```

Octopress blog could be seen through _username.github.com_ few minutes later.

If you want to deploy it to your own domain like me,
create a new CNAME record in domain name server.

e.g. I went to DNS manage in the Godaddy, and added _voice_ as the name, __CNAME__ as the type and _lawrencesun.github.com_ as the value.

When typing in the new subdomain(in my case, voice.lawrencesun.info), it will point to a Github 404.

Then run this to create a CNAME file in source directory:

``` ruby
echo '_yourdomain.com_(e.g. voice.lawrencesun.info)' >> source/CNAME
```  

Finally, run _rake generate_ and _rake deploy_ and git commit as well.

