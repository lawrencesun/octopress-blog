---
layout: post
title: "My Git Cheet Sheet"
date: 2014-01-08 21:08:18 +0000
comments: true
categories: Git 

keywords: git, rails, ruby, github
description: Cheet Sheet for Git
---

When I saw the Error on my rails sample_app and couldn't find the solution the other day, I wanted to abort the changes and intended to go back to the last commit. I searched on _stackoverflow_ and followed the instruction, ran something like 
	git reset --Hard 
Then... I messed up the files and commits, unsurprisingly.

So I decided to learn some basic command of __Git__ apart from 
	git add .
	git commit -m "I need a cupa"
	git push

After watching and practing Git command from __Git Real__ on [Code School](http://zfer.us/9yIBW), I generated a basic _cheat sheet_ for myself. Here it is.

* __Create__
```ruby
 # Start a new repo
 $ git init  
 # Clone existing repo
 $ git clone ssh://user@domain.com/rego.git
```

* __Local Change__
```ruby
 # Check what's changed
 $ git status 
 # Add all current changes to the next commit (staging area)
 $ git add .
 # Add some files
 $ git add <filename> 
 # Add files in /doc directory
 $ git add doc/
 # Add all txt files
 $ git add *.txt
 # Commit previously staged changes
 $ git commit -m "Desperately need coffee"
 # Commit all changes from tracked(not new) files  
 $ git commit -a -m "Coffee..."
 # Change the last commit
 $ git commit --amend
 # Show difference
 $ git diff
 # Show staged difference
 # git diff --staged
```

* __Remote__
```ruby
 # Add a remote
 $ git remote add <remote> <url>
 # Delete a remote
 $ git remote -mv <remote>
 # List all remote repos
 $ git remote -v
 # Push local branch to remote repo, -u to set default 
 $ git push -u <remote> <branch>
 # Pull changes down from the remote. Including fetch(sync) and merge. 
 $ git pull 
```

* __History__
```ruby
 # Show all commits
 $ git log
 # Show commits with graph
 $ git log --online --graph
 # Show who chaged what 
 $ git blame <filename>
```

* __Undo__
```ruby
 # Discard all the changes since last commit
 $ git checkout -- <filename>
 # Undo last commit and put changes into staging
 $ git reset --soft HEAD^
 # Undo last 2 commits and all changes
 $ git reset --hard HEAD^^
```

* __Tags and Branches__
```ruby
 # Mark current commit with a tag
 $ git tag <name>
 # List existing branches
 $ git branch
 # Create a new branch
 $ git branch <new_branch>
 # Create a new branch and switch to it
 $ git checkout -b <new_branch>
 # Switch HEAD branch
 $ git checkout <branch>
 # Check remote branches
 $ git branch -r
 # Delete a local branch
 $ git branch -d <branch>
 # Delete a remote <branch>
 $ git push origin <branch>
 # Clean up deleted remote branches
 $ git remote prune origin
```

* __Merge and Rebase__
```ruby
 # Merge <branch> into current HEAD
 $ git merge <branch>
 # Fetch(sync) remote but don't merge
 $ git fetch
 # Rebase current HEAD onto <branch>
 $ git rebase <branch> 
```

* __Config__
```ruby
 # Global user name and email
 $ git config --global user.name "name"
 $ git config --global user.email "email"
 # Alias
 $ git config alias.<name> <command>
 # Colour UI
 $ git config --global color.ui true
```