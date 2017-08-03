---
layout: post
title:  "Git Recipes"
date:   2017-07-31 18:44:25 -0300
categories: [recipe]
tags: [git, github]
author: Marcelo Foss
---
The official Git documentation has the fantastic **giteveryday** guide, that can be found here:  
[https://git-scm.com/docs/giteveryday](https://git-scm.com/docs/giteveryday)

This article reformats the referred guide.

### The most basic workflow
1. Checkout or create new branch 
```$git checkout -b <new_branch>```
2. Apply changes and commit
```$git add .``` and ```$git commit -m 'your comment'```
4. Merge changes from others 
```$git merge master```
5. Checkout master (or the branch you wish to merge into) 
```$git checkout master```
6. Merge the new branch 
```$git merge <new_branch>```
7. Delete the branch you no longer need or you can also continue to develop on the new branch, merging it in a later moment.

 
### For the individual Developer (Standalone)
A standalone individual developer does not exchange patches with other people, and works alone in a single repository.

#### Basics
* ```git-init``` to create a new repository.
* ```git-log``` to see what happened.
* ```git-checkout``` and ```git-branch``` to switch branches.
* ```git-add``` to manage the index file.
* ```git-diff``` and ```git-status``` to see what you are in the middle of doing.
* ```git-commit``` to advance the current branch.
* ```git-reset``` and ```git-checkout``` (with pathname parameters) to undo changes.
* ```git-merge``` to merge between local branches.
* ```git-rebase``` to maintain topic branches.
* ```git-tag``` to mark a known point.

#### Initiate a local repository
1. Use a tarball as a starting point for a new repository.
```
$ tar zxf frotz.tar.gz
$ cd frotz
$ git init
```
2. Add everything under the current directory.
```
$ git add . 
```
3. Commit all files to local repository
```
$ git commit -m "import of frotz source tree."
```
4. Make a lightweight, unannotated tag.
```
$ git tag v2.43
```

#### Create a topic branch and develop.
1. Create a new topic branch.
```
$ git checkout -b alsa-audio
$ edit/compile/test
```

2. Revert your botched changes in curses/ux_audio_oss.c.
```
$ git checkout -- curses/ux_audio_oss.c
```

3. You need to tell Git if you added a new file; removal and modification will be caught if you do git commit -a later.
```
$ git add curses/ux_audio_alsa.c
$ edit/compile/test
```

4. To see what changes you are committing.
```
$ git diff HEAD 
```

5. Commit everything, as you have tested, with your sign-off.
```
$ git commit -a -s
$ edit/compile/test
```

6. Look at all your changes including the previous commit.
```
$ git diff HEAD^
```

7. Amend the previous commit, adding all your new changes, using your original message.
```
$ git commit -a --amend
```

8. Switch to the master branch.
```
$ git checkout master
```

9. Merge a topic branch into your master branch.
```
$ git merge alsa-audio
```

10. Review commit logs; other forms to limit output can be combined and include -10 (to show up to 10 commits), --until=2005-12-10, etc.
```
$ git log --since='3 days ago'
```

11. View only the changes that touch what’s in curses/ directory, since v2.43 tag.
```
$ git log v2.43.. curses/ 
```

### Individual Developer (Participant)
A developer working as a participant in a group project needs to learn how to communicate with others, and uses these commands in addition to the ones needed by a standalone developer.

* ```git-clone``` from the upstream to prime your local repository.
* ```git-pull``` and ```git-fetch``` from "origin" to keep up-to-date with the upstream.
* ```git-push``` to shared repository, if you adopt CVS style shared repository workflow.
* ```git-format-patch``` to prepare e-mail submission, if you adopt Linux kernel-style public forum workflow.
* ```git-send-email``` to send your e-mail submission without corruption by your MUA.
* ```git-request-pull``` to create a summary of changes for your upstream to pull.

#### Clone the upstream and work on it. Feed changes to upstream.

1. Clone repository
```
$ git clone git://git.kernel.org/pub/scm/.../torvalds/linux-2.6 my2.6
$ cd my2.6
```

2. checkout a new branch <mine> from master.
```
$ git checkout -b mine master 
```

3. repeat as needed.
```
$ edit/compile/test; git commit -a -s
```

4. extract patches from your branch, relative to master,
```
$ git format-patch master 
```

5. and email them.
```
$ git send-email --to="person <email@example.com>" 00*.patch 
```

6. return to master, ready to see what’s new
```
$ git checkout master 
```

7. git pull fetches from origin by default and merges into the current branch.
```
$ git pull 
```

8. immediately after pulling, look at the changes done upstream since last time we checked, only in the area we are interested in.
```
$ git log -p ORIG_HEAD.. arch/i386 include/asm-i386 
```

9. check the branch names in an external repository (if not known).
```
$ git ls-remote --heads http://git.kernel.org/.../jgarzik/libata-dev.git 
```

10. fetch from a specific branch ALL from a specific repository and merge it.
```
$ git pull git://git.kernel.org/pub/.../jgarzik/libata-dev.git ALL 
```

11. revert the pull.
```
$ git reset --hard ORIG_HEAD 
```

12. garbage collect leftover objects from reverted pull.
```
$ git gc 
```

### Push into another repository.

1. mothership machine has a frotz repository under your home directory; clone from it to start a repository on the satellite machine.
```
satellite$ git clone mothership:frotz frotz 
satellite$ cd frotz
```

2. clone sets these configuration variables by default. It arranges ```git pull``` to fetch and store the branches of mothership machine to local ```remotes/origin/*``` remote-tracking branches.
```
satellite$ git config --get-regexp '^(remote|branch)\.' 
remote.origin.url mothership:frotz
remote.origin.fetch refs/heads/*:refs/remotes/origin/*
branch.master.remote origin
branch.master.merge refs/heads/master
```

3. arrange ```git``` push to push all local branches to their corresponding branch of the mothership machine.
```
satellite$ git config remote.origin.push \
	   +refs/heads/*:refs/remotes/satellite/* 
satellite$ edit/compile/test/commit
```

4. push will stash all our work away on ```remotes/satellite/*``` remote-tracking branches on the mothership machine. You could use this as a back-up method. Likewise, you can pretend that mothership "fetched" from you (useful when access is one sided).
```
satellite$ git push origin 
mothership$ cd frotz
mothership$ git checkout master
```

5. on mothership machine, merge the work done on the satellite machine into the master branch.
```
mothership$ git merge satellite/master
```

### Branch off of a specific tag.
1. create a private branch based on a well known (but somewhat behind) tag.
```
$ git checkout -b private2.6.14 v2.6.14
$ edit/compile/test; git commit -a
$ git checkout master
```
2. forward port all changes in private2.6.14 branch to master branch without a formal "merging". Or longhand git format-patch -k -m --stdout v2.6.14..private2.6.14 | git am -3 -k
```
$ git cherry-pick v2.6.14..private2.6.14 
```


An alternate participant submission mechanism is using the git request-pull or pull-request mechanisms (e.g as used on GitHub (www.github.com) to notify your upstream of your contribution.

