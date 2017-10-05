---
layout: post
title:  "Branching strategy, CI flow and release lifecycle"
date:   2017-09-22 19:45:31 -0300
categories: [article]
tags: [ruby, git, gitflow, alm]
author: Marcelo Foss
---
This post is about which factors influence the decision to choose what is the application release lifecycle, the Continuous integration flow, and the branching strategy for versioning the source code of a project.

I recently started a new solo project, and I was deciding a development workflow to use for that project. That involves a decision on how to release it, which CI flow to use, what is the branching approach to apply, which standards to follow. Some other questions where already answered, since are part of the default toolset I use: I use Git for version control, my preferred approach to develop software is BDD, and the technology stack is RoR (that is not relevant for that post).
I began by choosing the branching strategy to use with Git.
Git is really good on making the task of branching easy, nevertheless knowing when to branch and when to merge can still be tricky. Making it the wrong way can mine a project, and will raise issues when making new releases or fixing urgent bugs.

That's the motivation to write this post, I decided to spend some time working out exactly what factors are involved in such a decision.

### Best practices
What you always will need is that the mainline branch has a stable release.

### How often the software will be release
Is there any need to release the software in a specific frequency? Every month, every sprint, every 5 weeks? Or whenever it is ready to? Specific dates in the calendar? The answer for this questions depends not only to the team or company capacity to deliver, but are also related to commercial needs and aspects.  
The more frequent there is a release, the nearer your feature branches should be to the mainline. The relation is size of the change being shipped. The bigger or more complex the change is, more review, validation and testing needs to be applied.

### Testing
What is the maturity of the team? What is the level of automated tests? What is the confidence that the new feature will do or behave as expected? How QA is applied for the application, is there a dedicated QA team, an acepptance testing procedure? In which stage aceeptance testing is performed?
When the acceptance testing is applied? How many time between acceptance testing and the release is available to fix
There are lots of options around this, but it generally boils down to either early stream or late stream QA. The early variant means that a feature is tested before it is merged, the late variant means it’s tested afterwards. If your QA is early stream then you can probably get away with having your feature branch close to the mainline. If it’s late, then a release branch is probably where your QA should take place, so that failures can be rectified before they reach the mainline.
The only exception to that rule is if you have either particularly spare release cadence (so there’s time to fix things), or a reasonably high bug tolerance, for example if a project is pre-release or is an internal tool that isn’t mission critical. In these cases it’s generally wise to avoid additional long-lived branches and get straight to the mainline.
### Release Certainty
How likely is it that a given feature will be released, assuming that it passes QA?
Release planning is another subject entirely, but your branching strategy needs to support your process. They key is to avoid a feature hitting the mainline until it’s approved for release. Gitflow achieves this via the develop and release branches, but you could achieve the same effect with just a mainline and a release branch.


but it’s not necessarily the right fit for your team. There are many factors involved in deciding a branching strategy that may mean you need something different.

So why not use gitflow? It's a very successful strategy, it is well known, if the project grows, and other developers join, it is easy to grasp...


In this post I present the development model that I’ve introduced for some of my projects
I won’t talk about any of the projects’ details, merely about the branching strategy and release management.   

Choosing a Git Branching Strategy
Recently I was part of a team trying to decide on a branching strategy for a new project. I had some clear ideas about what I thought made sense, but I found it hard to explain to my colleagues exactly why. To avoid justifying my ideas with hand-waving and mumbling in future, I decided to spend some time working out exactly what factors are involved in such a decision.
Git does a wonderful job of making branching easy, but knowing when to branch and when to merge can still be tricky. Getting this wrong can cause headaches later on when it comes to releasing features or handling urgent bugs in already released code, so it’s certainly worth giving it some thought.
In 2010, Vincent Driessen proposed gitflow as one potential solution to this problem. It’s a very successful strategy that’s worth getting to grips with, but it’s not necessarily the right fit for your team. There are many factors involved in deciding a branching strategy that may mean you need something different.
First, some definitions. I’ll be talking about your mainline, meaning your repository’s default branch (usually master). I’ll also mention feature branches to refer to a branch where developers first commit code, that’s not the mainline.
Now, let’s look at the factors we need to consider when choosing a branching strategy.
Release Cadence
Do you ship at the end of every sprint whether it’s ready or not? Do you release code every day? Multiple times a day? Perhaps you release once a month, or just whenever you feel like it’s time. This is one of the things that varies most between different teams and it has a big impact on how you should structure your branches.
Put simply, the more often you release, the nearer your feature branches should be to the mainline. GitHub flow, used by GitHub is a good example of this — developers branch from the mainline, develop their feature and then merge back to the mainline for immediate release. If, however, you release once every two weeks it makes more sense to merge to a ‘holding’ branch (like gitflow’s develop and release branches) before code reaches the mainline.
Testing
How confident are you that, once a feature has been developed, it will actually do what it was supposed to do? How do you confirm that the code works as expected? If you have some form of quality assurance or acceptance testing, at what stage is it done? There are lots of options around this, but it generally boils down to either early stream or late stream QA. The early variant means that a feature is tested before it is merged, the late variant means it’s tested afterwards. If your QA is early stream then you can probably get away with having your feature branch close to the mainline. If it’s late, then a release branch is probably where your QA should take place, so that failures can be rectified before they reach the mainline.
The only exception to that rule is if you have either particularly spare release cadence (so there’s time to fix things), or a reasonably high bug tolerance, for example if a project is pre-release or is an internal tool that isn’t mission critical. In these cases it’s generally wise to avoid additional long-lived branches and get straight to the mainline.
Release Certainty
How likely is it that a given feature will be released, assuming that it passes QA?
Release planning is another subject entirely, but your branching strategy needs to support your process. They key is to avoid a feature hitting the mainline until it’s approved for release. Gitflow achieves this via the develop and release branches, but you could achieve the same effect with just a mainline and a release branch.
In Conclusion
Every branching strategy is a variation on the theme of keeping code out of your mainline until you want it there, balanced against the management overhead caused by having lots of unmerged branches. If your instinct is to avoid merging until the last minute, think about why. Perhaps you need to schedule development more carefully. Conversely, if you’re having to undo merges and cherry pick things a lot then perhaps you’re too keen to merge.
Ultimately, you have to be pragmatic. A complex strategy that mirrors your processes perfectly is only workable if your tooling is up to the job. It’s wise to start simply and add long-lived branches as you need them. Also bear in mind that git is very forgiving. Mistakes happen and they can be rectified — it doesn’t necessarily mean that your strategy is faulty.

[https://www.atlassian.com/git/tutorials/comparing-workflows](https://www.atlassian.com/git/tutorials/comparing-workflows)

[http://nvie.com/posts/a-successful-git-branching-model/](http://nvie.com/posts/a-successful-git-branching-model/)

[https://guides.github.com/introduction/flow/](https://guides.github.com/introduction/flow/)

! http://ndemengel.github.io/2014/05/15/continuous-delivery-recipes-branching-strategies/


https://medium.com/@jezhalford/choosing-a-git-branching-strategy-cd7a774a68a1


https://fjorgemota.com/git-flow-uma-forma-legal-de-organizar-repositorios-git/

http://guides.beanstalkapp.com/deployments/deploy-with-capistrano.html

https://medium.com/wolox-driving-innovation/ruby-on-rails-continuous-integration-with-jenkins-and-docker-compose-8dfd24c3df57

https://mattbrictson.com/rails-continuous-integration

http://semver.org/
