---
layout: post
title:  "Rails architecture"
date:   2017-11-07 19:45:31 -0300
categories: [article]
tags: [ruby, ror, architecture]
author: Marcelo Foss
---
From wikipedia:
*"Ruby on Rails, or Rails, is a server-side web application framework written in Ruby under the MIT License. Rails is a model–view–controller (MVC) framework, providing default structures for a database, a web service, and web pages. It encourages and facilitates the use of web standards such as JSON or XML for data transfer, and HTML, CSS and JavaScript for display and user interfacing. In addition to MVC, Rails emphasizes the use of other well-known software engineering patterns and paradigms, including convention over configuration (CoC), don't repeat yourself (DRY), and the active record pattern."*

## Ruby on Rails architecture has the following features:
* Model-View-Controller architecture.
* Representational State Transfer (REST) for web services.
* Supports the major databases (MySQL, Oracle, MS SQL Server, PostgreSQL, IBM DB2, and more).
* Open-source server side scripting language.
* Convention over configuration
* Scripts generators to automate tasks.
* Use of YAML machine, which is a human-readable data serialization format.

## Rails components
* Action Mailer
* Action Pack
  * Action Controller
  * Action Dispatcher
  * Action View
* Active Model
* Active Record
* Active Resource
* Active Support
* Railties

http://curriculum.railsbridge.org/intro-to-rails/rails_architecture

http://halyph.com/blog/2015/04/04/rails-architecture-and-scalability-issues.html

https://medium.com/@fbzga/clean-architecture-in-ruby-7eb3cd0fc145

https://blog.codeship.com/architecting-rails-apps-as-microservices/

http://adrianmejia.com/blog/2011/08/11/ruby-on-rails-architectural-design/
