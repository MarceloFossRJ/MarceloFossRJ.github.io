---
layout: post
title: Capistrano recipes
date:   2018-10-20 18:44:25 -0300
category: [recipe]
tags: [deploy, capistrano, rake, deployment, release]
author: Marcelo Foss
intro: |
  [Capistrano](https://github.com/capistrano/capistrano) is a framework for building automated deployment scripts. Although Capistrano itself is written in Ruby, it can easily be used to deploy projects of any language or framework, be it Rails, Java, or PHP.
---

# Capistrano: A deployment automation tool built on Ruby, Rake, and SSH.

*Note: This article is for Capistrano version 3.x*

Capistrano is a framework for building automated deployment scripts. Although Capistrano itself is written in Ruby, it can easily be used to deploy projects of any language or framework, be it Rails, Java, or PHP.

Once installed, Capistrano gives you a `cap` tool to perform your deployments from the comfort of your command line.

```
$ cd my-capistrano-enabled-project
$ cap production deploy
```

When you run `cap`, Capistrano dutifully connects to your server(s) via SSH and executes the steps necessary to deploy your project. You can define those steps yourself by writing [Rake](https://github.com/ruby/rake) tasks, or by using pre-built task libraries provided by the Capistrano community.

Tasks are simple to make. Here's an example:

```ruby
task :restart_sidekiq do
  on roles(:worker) do
    execute :service, "sidekiq restart"
  end
end
after "deploy:published", "restart_sidekiq"
```

## Quick start

### Requirements

* Ruby version 2.0 or higher on your local machine (MRI or Rubinius)
* A project that uses source control (Git, Mercurial, and Subversion support is built-in)
* The SCM binaries (e.g. `git`, `hg`) needed to check out your project must be installed on the server(s) you are deploying to
* [Bundler](http://bundler.io), along with a Gemfile for your project, are recommended

### Install the Capistrano gem

Add Capistrano to your project's Gemfile using `require: false`:

``` ruby
group :development do
  gem "capistrano", "~> 3.11", require: false
end
```

Then run Bundler to ensure Capistrano is downloaded and installed:

``` sh
$ bundle install
```

### "Capify" your project

Make sure your project doesn't already have a "Capfile" or "capfile" present. Then run:

``` sh
$ bundle exec cap install
```

This creates all the necessary configuration files and directory structure for a Capistrano-enabled project with two stages, `staging` and `production`:

```
├── Capfile
├── config
│   ├── deploy
│   │   ├── production.rb
│   │   └── staging.rb
│   └── deploy.rb
└── lib
    └── capistrano
            └── tasks
```

To customize the stages that are created, use:

``` sh
$ bundle exec cap install STAGES=local,sandbox,qa,production
```

Note that the files that Capistrano creates are simply templates to get you started. Make sure to edit the `deploy.rb` and stage files so that they contain values appropriate for your project and your target servers.

### Command-line usage

``` sh
# list all available tasks
$ bundle exec cap -T

# deploy to the staging environment
$ bundle exec cap staging deploy

# deploy to the production environment
$ bundle exec cap production deploy

# simulate deploying to the production environment
# does not actually do anything
$ bundle exec cap production deploy --dry-run

# list task dependencies
$ bundle exec cap production deploy --prereqs

# trace through task invocations
$ bundle exec cap production deploy --trace

# lists all config variable before deployment tasks
$ bundle exec cap production deploy --print-config-variables
```
