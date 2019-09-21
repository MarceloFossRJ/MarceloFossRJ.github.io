---
layout: post
title:  "Git Reference Card"
date:   2017-07-26 18:44:25 -0300
categories: [reference card]
tags: [git, github, version control]
author: Marcelo Foss
---
Git is a distributed version control system that uses a local repository and a remote repository.  
A Git local repository is composed of 3 different "trees" or "spaces":  
* Working Directory: holds the actual files.
* Index: which acts as a staging area
* Local repository or HEAD: which points to the last commit you've made.  

A git remote repository:  
* Remote repository: Network or Cloud (github, butbucket,...)

<img src="/assets/images/git_1.png" alt="Git workflow" style="width: 600px;"/>

Additionally a file can have 3 states:
* modified
* staged
* committed

## Git basics
### Configuring your name and email address  
```
$git config --global user.name "Your Name"
$git config --global user.email you@example.com
```

### Creating a Git repository  
To create and initialize the local Git repository (.git) in the current folder:  
```
$git init
```

To clone an existing repository:  
```
$git clone git://github.com/marcelofossrj/aca-rails.git
```
It pulls down every version of every file for the history of the project.  

* Allowed protocols for remotes:  
	* ssh:// - pull push
	* http[s]:// - pull	 
	* git:// - pull	 
	* file:// - pull push  
	(rsync:// and ftp:// are not recommended)

## Recording changes to the repository
Each file can be in one of two states:  
* Tracked
* Untracked

### Ignoring files
Set up a .gitignore file, in the folder the repository was initialized, with glob patterns (simplified regular expressions <a href='https://en.wikipedia.org/wiki/Glob_(programming)' target='\_blank'>https://en.wikipedia.org/wiki/Glob_(programming)</a>)

### Tracking newly created files, or Staging modified files:
To add a file to staging or index:
```
$git add <filepattern>
$git add .
$git add <filename>
```

### Committing your changes
Every commit is uniquely identified by its ID:  
Commit ID = checksum of content (any number of files) + author + date + log message + ID of previous commit.

To Commit your staged changes:
```
$git commit -m "My first commit!" # The file is committed to the HEAD, but not in your remote repository yet.
```

Skipping the staging area
```
$git commit -a  #commits only tracked files
$git commit -am "commiting without staging"
```

### Viewing your staged and unstaged changes

To list new or modified files not yet committed
```
$git status
```

To see what you have changed
```
$git diff  # To see only changes that are still unstaged.
$git diff --cached  # To see what you have staged that will go into your next commit
$git diff HEAD  # To see all staged and unstaged file changes
```

To see the changes between two commit ids
```
$git diff commit_1 commit_2
```

To list the change dates and authors for a file
```
$git blame [file]
```

### Viewing the commit history

To list the commits made in the repository
```
$git log
```

Show the diff introduced in each commit
```
$git log -p
```

#### Limiting log output

Time-limiting options  (dates can be absolute: "2008-01-15" or relative: "2 weeks")
```
--since #after the specified date
--until #before the specified date
```
 Search criteria:
```
--author
--committer
```
Grep option:
```
--grep #search for keywords in the commit messages
--all-match #match commit with both author and grep options, for example
```

### Removing files

Remove a file from your tracked files (from your staging area) and also from your working directory
```
$git rm
```

Remove a file from your staging area but keep it in your working directory
```
$git rm --cached
$git rm log/\*.log
```
The backslash is necessary because Git does its own filename expansion in addition to your shell's filename expansion.

### Reversing changes
Undo Add or unstaging a file
```
$git reset HEAD <file>
```

Undo a commit or undo a merge or pull
```
$git reset --hard         # Revert everything to the last commit
$git reset --hard HEAD~3  # Rewind the master branch 3 commits.
```

To revert a file back to what it looked like when you last committed
```
$git checkout -- <file>
```

## Working with remotes

### Showing your remotes
Show the URL that Git has stored for the shortname to be expanded to:
```
$git remote -v
```
origin is the default shortname Git gives to the server (or repo) you clone from.

### Adding remote repositories
```
$git remote add <shortname> <url>
$git remote add origin https://github.com/MarceloFossRJ/aca-rails.git #git uses origin as default name for a remote
```

## Synchronizing repositories
To get the latest changes from origin with no merge. Git will not fetch data in your working directory (only in your Git directory) – you still have the files in your working directory. It fetches it in a separate (remote) branch: your master (or develop or whatever) branch is different from origin/master branch.
```
$git fetch
```

To fetch the latest changes from origin and merge
It incorporates changes from a remote repository into the current branch. More precisely, git pull runs git fetch and calls git merge to merge the retrieved branch heads into the current branch.
```
$git pull  # It does commit!
```

Push your master branch to your origin server:
```
$git push origin master
```
This command works only:
if you cloned from a server to which you have write access and
if nobody has pushed in the meantime

## Tagging
A tag marks a commit.

#### Listing your tags
```
$git tag
```

#### Creating tags
Git has 2 main types of tags:  
* **lightweight**: is just a named pointer to a specific commit. This type of tag does not allow you to store any information that specific to the tag.
* **annotated**: is a regular Git object, can be referred because they have their own SHA key and has its own commit hash. It allows to store a tag message, GPG sign it, and the tagger is stored.

Annotated tags are meant for release while lightweight tags are meant for private or temporary object labels.
```
$ git tag -s -a -m <message> <tagname>
````
`-s` will GPG sign your tag.

`-a` will create an annotated tag. Git will prompt for an annotation.  

`-m <message>` will add a tag message. Git will *not* prompt for an annotation   

If you need to amend / fix / replace an existing tag, you can use the ```-f``` parameter to overwrite the current tag.

```
$git tag <tagname>   #Tags at the current revision without a message.
$git tag -a <tagname>   #Git will prompt for an annotation
$git tag -a -m <msg> <tagname>   #Tags the commit with the provided message
$git tag -m <msg> <tagname>   #Behaves as passed the -a flag for annotation and uses the message.
```

#### Verifying tags

Verify the signature.
```
$git tag -v <tagname>
```

Tagging after the fact
```
$git tag -a <tagname> <commit>
$git tag -s <tagname> <commit>
$git tag <tagname> <commit>
```

#### Sharing tags

By default, the git push command doesn't transfer tags to remote servers.
```
$git push origin --tags
```

## Git branches
A branch is a parallel version of a codebase. Is used to develop features isolated from each other.  
The repository is initiated with the master branch, which is the "default" git branch.  
Create new branches to develop new features, and when finished merge them back to the master branch.

A branch in Git is a file that contains the 40 hex-digit SHA-1 checksum of the commit it points to.

The difference between a branch and a tag is that when you make a commit with the master branch currently checked out, master will move to point to the new commit, effectively adding it to the branch. Tags don't.

#### List branches
```
$git branch     # List all local branches
$git branch -av # List all local and remote branches
$git branch -r  # List all remote branches
```
### Basic branching
Create a new branch (from current branch):
```
$git branch <branch_name>
```

Switch to a branch and update the working directory
```
$ git checkout <branch_name>
```

Create a branch and switch to it at the same time:
```
$git checkout -b <branch_name>  # branch and checkout
```

Delete a branch
```
$git branch -d <branch_name>
```

Merge branch <branch_name_1> into <branch_name_2>
```
$git checkout <branch_name_2>
$git merge <branch_name_1>
```

## A basic workflow
1. Checkout or create new branch  
`$git checkout -b <new_branch>`  
2. Apply changes and commit  
`$git commit -a -m 'your comment'`    
4. Merge changes from others   
`$git merge master`  
5. Checkout master (or the branch you wish to merge into)   
`$git checkout master`   
6. Merge the new branch   
`$git merge <new_branch>`   
7. Delete the branch you no longer need or you can also continue to develop on the new branch, merging it in a later moment.   

## Merge conflicts

If there is a merge conflict (automatic merge failed):   
* Manually resolve each conflict section (<<<<<<< markers)
* Mark the file as resolved by staging it (git add)
* Finalize the mere commit by typing git commit
* You can use a graphical tool to resolve the conflicts:
```
$git mergetool
```
