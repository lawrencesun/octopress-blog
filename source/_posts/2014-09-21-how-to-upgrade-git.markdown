---
layout: post
title: "How to upgrade git"
date: 2014-09-21 17:25:08 +0800
comments: true
categories: Git
keywords: git, ruby, homebrew
description: 
---

When I intended to install ImageMagick via homebrew, and found some issues by runing `brew doctor`. One was telling me that the git was out of date. 

When installing Git for Mac OS via XCode, it points Git to usr/bin/git.
run `which git` to check out the directory.

If we want to keep git up to date, we could install/upgrade git using homebrew. 

Add the line in ~/.bash_profile 

`export PATH="/usr/local/bin:$PATH"`

Then run `brew install git` and `brew update git`.