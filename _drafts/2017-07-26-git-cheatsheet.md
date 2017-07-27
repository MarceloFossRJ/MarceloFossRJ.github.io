---
layout: post
title:  "Git Cheatsheet"
date:   2017-07-24 18:44:25 -0300
categories: [cheat sheet]
tags: [git, github]
author: Marcelo Foss
---
### Where shall we start?
Well, we’ll start at the beginning of course! In the beginning, Git created init and clone. Both of which give you a repository in which you can manage your source code. The difference being of course that you would use the init command to create a repository from scratch, whereas you’d use clone to literally clone, or copy an existing repository into the directory you ran the command from. Once this is done, we’re able to start our workflow! Here are the two examples in action, on the ZipRebel project:

``` $git init myProject  
Initialized empty Git repository in /Users/sjmaple/myProject/.git/``` 
And as a clone:
```$ git clone https://github.com/zeroturnaround/ziprebel.git
Cloning into 'ziprebel'...
remote: Counting objects: 94, done.
remote: Total 94 (delta 0), reused 0 (delta 0), pack-reused 94
Unpacking objects: 100% (94/94), done.
Checking connectivity... done.```

https://zeroturnaround.com/rebellabs/git-commands-and-best-practices-cheat-sheet/
https://scotch.io/bar-talk/git-cheat-sheet

