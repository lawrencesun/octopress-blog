---
layout: post
title: "如何合并多个Git commit"
date: 2014-07-15 22:06:07 +0800
comments: true
categories: Git
keywords: git, commit, squash, rebase
description: squash several git commits into one, no branch
---
今天把之前做的一个rails项目升级到Twitter Bootstrap 3.2，结果问题一大堆。主要是class和id名称的改动，以及响应式布局的调整。最后在Heroku部署完成之后，发现原来的“点赞”图标无法正确显示，在stackoverflow翻阅了很多相关问答，和我有同样问题的人不少。但是按照这些方法去尝试，没有一个能有效解决。最终我改用了fonts awesome。

在这个期间，遇到了不少git方面的问题，看来之前还是没有把基础打好。

一个问题是由于不断尝试不同的方法，导致commit过多，非常混乱。于是想把多个commit合并到一起。具体方法如下：

<!-- more -->

首先`git log`查看目前的commit, 假设按照时间先后顺序有 a, b, c, d 三个commit.

现在要把四个合并到一起，使用`rebase`这个命令。

```ruby
$ git rebase -i HEAD~4

pick 01d1124 a
pick 6340aaa b
pick ebfd367 c
pick 30e0ccb d

# Rebase 60709da..30e0ccb onto 60709da
#
# Commands:
#  p, pick = use commit
#  e, edit = use commit, but stop for amending
#  s, squash = use commit, but meld into previous commit
#
# If you remove a line here THAT COMMIT WILL BE LOST.
# However, if you remove everything, the rebase will be aborted.
#
```
接着，把前四行进行如下替换：

```ruby
pick 01d1124 a
squash 6340aaa b
squash ebfd367 c
squash 30e0ccb d
```

Git将会把所有四个commit合并到第一个里面去。保存退出后，会出现：

```ruby
# This is a combination of 4 commits.
# The first commit's message is:
a
# This is the 2nd commit message:
b
# This is the 3rd commit message:
c
# This is the 4th commit message:
d
......
```
修改新的commit message后再次保存退出，合并完成。

如果在过程中出现冲突，可以使用`git rebase --abort`命令。

第二个问题是我在git push时候出现 'Everything is up to date'。通过`git branch`查询发现当前的branch是* (no branch).

出现这种情况，首先`git reflog`， 找到最新的一个commit，然后创建一个临时branch.

`git checkout -b temp-branch <commit-id>` 之后合并到master。
