---
layout: post
title:  "Rails code review"
date:   2017-11-05 19:45:31 -0300
categories: [article]
tags: [ror, code review, code quality]
author: Marcelo Foss
---
# Code reviews?
In general we all want that a software do what it is intended to do, but above that we also want to have it well written code, that is easy to scale, easy to maintain, is well documented, and tested.
Performing code reviews in most of the cases is an easy way to:
* keep code standards,
* teach/coach developers,
* ensure best practices are applied.

# How it happens?
A code review takes place normally when a developer finished working in an user story, issue, or task.
Another developer looks over the code and considers questions like:
* Are the user story goals accomplished? (or is the issue solved?)
* Are there any logic issue?
* Are the style guidelines and standards being followed?
* Do we have tests? Are the tests covering all scenarios?

So it involves one or more developers examining source code they didn't write and providing feedback to the author, both negative and positive.   
It should be a part of the team process, and strongly recommended to have it done after having all automated tests passed, and before is merged to working branch.  

It hard, it takes time, but has lots o benefits that pays off, like:
* Code reviews share knowledge
* Code reviews make for better estimates
* Code reviews reduces dependency
* Code reviews mentor new developers

# Best practices
* Rotate reviewers  
Focus on decentralizing the process so that no one is a bottleneck, and do not let the author choose the reviewer. This also ensures good coverage for code review across the team. Some teams even do two code reviews before merging the code.

* Review before merging  
Like mentioned before, do not merge the code to the working branch before doing the review, normally the time spend fixing a bad merge is going to be bigger (not to say the stress).

* Do not use reviews from an audit perspective  
Using reviews from an audit perspective is not the best approach for the team aura. Use the perspective that the developer should make an extra effort to ensure that the code is following the standards and best practices, that all tests are passing, so the review will go smoothly. That mindfulness also tends to make the coding process itself go smoother and, better and faster.


# Some Rails guidelines
Rails code review guidelines
[https://github.com/eliotsykes/rails-code-review][https://github.com/eliotsykes/rails-code-review]

[https://blog.planetargon.com/entries/ruby-on-rails-code-audits-8-steps-to-review-your-app][https://blog.planetargon.com/entries/ruby-on-rails-code-audits-8-steps-to-review-your-app]

[https://www.sitepoint.com/the-importance-of-code-reviews/][https://www.sitepoint.com/the-importance-of-code-reviews/]


1. All functions should be between less than 8 lines.
2. No function should have more than two levels of indent.
3. Names should be communicative and explicit.  
  3a. Variables in short scopes should have short names.   
  3b. Variables in long scopes should have long names.  
  3c. Functions in long scopes should have short names.  
  3d. Functions in short scopes should have long names.  
4. The code should read like well written prose and have very few comments.
5. As you read the code you should not have to look up the definitions of the variables and functions being called. They should be obvious.
6. Function calls should have 3 or fewer arguments, with a preference for fewer.
7. Unit test coverage should be close to 100%, and the programmer should know the coverage number.
8. Unit tests should be short, easy to read, and easy to understand. You should be able to understand the program by reading the tests.
9. The tests should execute very quickly. Long running tests are a symptom of carelessness.

As for a quality checklist, I err on the side of beauty:

* Does the code look beautify on the page? (I realize this is subjective, but to someone who "gets it" I maintain it's the best measure of excellence.)
* Are semantics consistent throughout the project?
* ActiveModel if appropriate
* Are migrations used to their full potential?
* Scopes and relations in the proper context?
* Uses virtual attributes?
* Complex object creation is handled in a factory?
* Effectively uses meta programming to make the project clean and tidy?
* Views are logic-free?
* Finally, another subjective but important item - code feels like the developer loves the project and wants to provide it with the best care possible.
