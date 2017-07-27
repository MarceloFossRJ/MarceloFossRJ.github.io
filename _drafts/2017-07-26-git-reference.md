---
layout: post
title:  "Git reference card"
date:   2017-07-24 18:44:25 -0300
categories: [reference card]
tags: [git, github]
author: Marcelo Foss
---
Git start from the concept that a file in a repository has 3 states:  
* modified	
* staged
* committed

Staging area ("cache") = index of what will go into your next commit  
Staging files = adding snapshots of them to the staging area  
Committing files = taking them as they are in the staging area, storing them permanently in Git compressed database in the Git directory.  

### Git basics
Configuring your name and email address  
{% highlight ruby %}
$git config --global user.name "Your Name"
$git config --global user.email you@example.com
{% endhighlight %}

#### Getting a Git repository  
To create and initialize the Git directory (.git) in the current folder:  
{% highlight ruby %}
$git init 
{% endhighlight %}

To clone an existing repository:  
{% highlight ruby %}
$git clone git://github.com/fniessen/repo.git
{% endhighlight %}
It pulls down every version of every file for the history of the project.  

* Allowed protocols for remotes:  
	* ssh:// - pull push
	* http[s]:// - pull	 
	* git:// - pull	 
	* file:// - pull push  
	(rsync:// and ftp:// are not recommended)

### Recording changes to the repository

Each file can be in one of two states:  
* Tracked
* Untracked

To check the status of your files
{% highlight ruby %}
$git status
{% endhighlight %}

#### Tracking newly created files, or Staging modified files

Stage files (the version of the files as it is at that time you run the command) to the index:
{% highlight ruby %}
$git add <filepattern>
{% endhighlight %}
#### Ignoring files

Set up a .gitignore file, with glob patterns (simplified regular expressions)

#### Viewing your staged and unstaged changes

See what you have changed but not yet staged:
{% highlight ruby %}
$git diff
{% endhighlight %}
doesn't show all the changes made since your last commit – only changes that are still unstaged.

See what you have staged that will go into your next commit
{% highlight ruby %}
$git diff --cached                     # --staged is a synonym
{% endhighlight %}
compares your staged changes to your last commit

### Committing your changes

Commit your staged changes:
{% highlight ruby %}
$git commit -m "My first commit!"
{% endhighlight %}

Skipping the staging area
{% highlight ruby %}
$git commit -a
{% endhighlight %}
automatically stage every file that is already tracked (saving all the changes into a single commit).

### Removing files

Remove a file from your tracked files (from your staging area) and also from your working directory
{% highlight ruby %}
$git rm
{% endhighlight %}

Remove a file from your staging area but keep it in your working directory
{% highlight ruby %}
$git rm --cached
$git rm log/\*.log
{% endhighlight %}

The backslash is necessary because Git does its own filename expansion in addition to your shell's filename expansion.

### Moving files
{% highlight ruby %}
git mv
{% endhighlight %}

### Viewing the commit history

List the commits made in the repository
{% highlight ruby %}
git log
{% endhighlight %}

Show the diff introduced in each commit
{% highlight ruby %}
git log -p
{% endhighlight %}

#### Limiting log output

Time-limiting options:
{% highlight ruby %}
--since
{% endhighlight %}

after the specified date
{% highlight ruby %}
--until
{% endhighlight %}
before the specified date

Dates are absolute ("2008-01-15") or relative ("2 weeks").

Search criteria:
{% highlight ruby %}
--author
--committer
{% endhighlight %}

Grep option:

--grep
search for keywords in the commit messages
--all-match
match commit with both author and grep options, for example
(no term)
--pretty
Undoing things

XXX

Changing your last commit

Fix the commit message:

git commit --amend
Unstaging a staged file

Change which commit the current branch is pointing to.

git reset HEAD <file>
Unmodifying a modified file

Revert a file back to what it looked like when you last committed

git checkout -- <file>
Working with remotes

Showing your remotes

Show the URL that Git has stored for the shortname to be expanded to:

git remote -v
origin is the default shortname Git gives to the server (or repo) you clone from.

Adding remote repositories

git remote add <shortname> <url>
Fetching and pulling from your remotes

Pull down all the data from the remote project that you don't have yet in your repository (and update the remote refs):

git fetch
Git will not fetch data in your working directory (only in your Git directory) – you still have the files in your working directory. It fetches it in a separate (remote) branch: your master (or develop or whatever) branch is different from origin/master branch.

Automatically fetch and then merge a remote branch into your current branch:

git pull                                # It does commit!
It is actually a bad idea to do both at the same time: it is highly recommended to do manually a fetch and then do a merge, unless you're the only person working at that repository. If pull blows up on you, it's a little hard to get out of it.

Pushing to your remotes

Push your master branch to your origin server:

git push origin master
This command works only:

if you cloned from a server to which you have write access and
if nobody has pushed in the meantime
Tagging

A tag marks a commit.

Listing your tags

git tag
Creating tags

2 main types of tags:

lightweight = just a pointer to a specific commit
annotated = pointer to a tag object
Annotated tags

Create an annotated tag (full object):

git tag -a <tagname>
Signed tags

Sign your tags with GPG:

git tag -s <tagname>
Lightweight tags

Create a pointer to the current commit.

git tag <tagname>
Verifying tags

Verify the signature.

git tag -v <tagname>
Tagging after the fact

git tag -a <tagname> <commit>
git tag -s <tagname> <commit>
git tag <tagname> <commit>
Sharing tags

By default, the git push command doesn't transfer tags to remote servers.

git push origin --tags
Tips and tricks

Auto-completion

source ~/git-completion.bash
Git aliases

Git branching
What a branch is

Branches are just names for commits.

Git stores data as a series of snapshots.

Stage a file:

Checksum (SHA-1 hash) the file
Store that version of the file in the Git repository
Add that checksum to the staging area
Default branch name: master (pointer).

Create a new branch (from current branch):

git branch testing
Current branch (that's what's checked out): HEAD (symbolic ref, pointing to a branch – or to a commit, when HEAD is detached).

This is a lot different than HEAD in Subversion or CSV.

Switch to an existing branch:

Move the HEAD pointer to point to the branch,
Revert the files in your working directory back to the snapshot that the branch points to (overwrites working dir!).
git checkout testing
Because a branch in Git is actually a simple file that contains the 40 hex-digit SHA-1 checksum of the commit it points to, branches are cheap to create and destroy. Creating a new branch is as quick and simple as writing 41 bytes to a file (40 characters and a newline).

The main difference between a branch and a tag is branches move, tags don't: when you make a commit with the master branch currently checked out, master will move to point to the new commit, effectively adding it to the branch.

Commit ID = checksum of content (any number of files) + author + date + log message + ID of previous commit.

Every commit's history can be uniquely identified by its ID.

Basic branching and merging

Basic branching (basic workflow)

Keep master clean, in a production-ready state at all times.

Isolate your work (for everybody else's work or your own work).

Create a branch and switch to it at the same time:

git checkout -b feature                 # branch and checkout
Naming convention: feature/cool-new-feature (or hotfix/bug-157). Or feature/username/cool-new-feature?

Do your work (edit, test, add, commit).

make check                              # run the tests
git commit -a
Pull updates from other people (merge master, test).

git merge master
The usual practice is to first merge master back into feature to make sure it has all the other stuff that master may have acquired till now.

Share it (merge back into your master branch, that is pull in the work you did in a branch):

git checkout master                     # branch you wish to merge into
git merge feature                       # branch you merge in
If there is no divergent work to merge together (some local development going on in master, unrelated to feature), git merge is a "fast forward": move the pointer forward to an upstream commit of the commit you're on.

Note -- To avoid cluttering the Git tree with intermediate commits, merge the branch with:

git merge --squash feature
It will look as if you had made all the changes in just one commit. Hence, this only makes sense for trivial things that should have been a single commit all along. Otherwise, you loose the logic between the different commits you've done…

Delete a branch you no longer need:

git branch -d feature
You can also continue to develop on the feature branch, once again merging it at some later point in time.

Basic merging

3-way merge when the commit on the branch you're on isn't a direct ancestor of the branch you're merging in:

Git creates a new snapshot that results from this 3-way merge
Git automatically creates a new commit that points to it ("merge commit", having more than one parent)
Git determines the best common ancestor, unlike CVS or Subversion.

Basic merge conflicts

If there is a merge conflict (automatic merge failed),

Manually resolve each conflict section (<<<<<<< markers)
Mark the file as resolved by staging it (git add)
Finalize the mere commit by typing git commit
You can use a graphical tool to resolve the conflicts:

git mergetool
Branch management

List your current (local) branches:

git branch
Show all the remote branches:

git branch -r
Show all the branches:

git branch -a


https://zeroturnaround.com/rebellabs/git-commands-and-best-practices-cheat-sheet/
https://scotch.io/bar-talk/git-cheat-sheet

