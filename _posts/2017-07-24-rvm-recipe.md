---
layout: post
title:  "The RVM Recipe"
date:   2017-07-24 17:12:01 -0300
categories: [recipe]
tags: []
author: Marcelo Foss
---
Ruby Version Manager, aka RVM, is a command line tool designed to manage multiple installations of Ruby on the same device.
In addition to allowing you to install and use multiple Ruby versions, it has other interesting features that you can use to maintain organization between projects with different gems.
In this article I will explain my basic workflow and also some tips on using RVM.
I do suppose that RVM is already installed.

## RVM Basic Commands
Basic syntax: rvm, a category, and a command for that category.

To list known rubies, or the ruby versions that are available to install:
{% highlight ruby %}
$rvm list known
{% endhighlight %}

To install a Ruby version:
{% highlight ruby %}
$rvm install 2.3.3                # Latest known patch level
$rvm install 1.9.3-p551           # Patchlevel 551
{% endhighlight %}

To list all rubies and gemsets installed in your computer
{% highlight ruby %}
$rvm list                   # List rubies only
$rvm list gemsets   # List rubies and gemsets
$rvm gemset list     # List gemsets for current ruby
{% endhighlight %}

Selecting Ruby to work
{% highlight ruby %}
$rvm use 2.3.3                    # For current session only
{% endhighlight %}

Defining the default version to use (you can refer to it as default)
{% highlight ruby %}
$rvm 2.3.3 --default     
{% endhighlight %}

Using the default ruby version
{% highlight ruby %}
$rvm use default
{% endhighlight %}

## Gemsets
RVM by default allows creating multiple environments for one ruby - called *gemsets*.
Gemsets is a feature that allows you manage gem versions, so it is possible to define a package (or set)of gems with a specific version. When assigning a gemset to a project you ensure that you have an isolated environment acessing the correct version of each gem.

During installation of Ruby, RVM creates two gemsets:
- default - automatically selected when no @gemset specified: rvm use 1.9.3
- global  - super gemset, inherited by all other gemsets for the given ruby

To create a gemset:
{% highlight ruby %}
$rvm gemset create gemset_name
{% endhighlight %}

Using a gemset:
{% highlight ruby %}
$rvm gemset_name
{% endhighlight %}
or
Gemsets can be specified together with ruby name using gemsets separator(@):
{% highlight ruby %}
$rvm gemset version@gemset_name
{% endhighlight %}
or
{% highlight ruby %}
$ruby-2.3.3@my-project
{% endhighlight %}

To create a gemset and already use it:
{% highlight ruby %}
$rvm --create use version@gemset_name
{% endhighlight %}

All gems that you install when using gemset will be confined to it.
In the list of gems will appear only the gems of the gemset and those of the global gemset.

If you want to install a new gem to the global gemset, use:
{% highlight ruby %}
$rvm gemset use global
$gem install name_of_the_gem
{% endhighlight %}

To see which gemset is being used:
{% highlight ruby %}
$rvm gemset name
{% endhighlight %}

Listing all available gemsets for the version:
{% highlight ruby %}
$rvm gemset list
{% endhighlight %}

Deleting a gemset and the gems contained in it (-force is not to ask for confirmation):
{% highlight ruby %}
$rvm --force gemset delete gemset_name
{% endhighlight %}

Where are the gems installed? Type:
{% highlight ruby %}
$gem env
{% endhighlight %}

That will result in something like:
{% highlight ruby %}
RubyGems Environment:
…
  - GEM PATHS:
     - /Users/foss/.rvm/gems/ruby-2.3.1@jekill
     - /Users/foss/.rvm/gems/ruby-2.3.1@global
{% endhighlight %}

In my case (using OS X El Capitan)
I have all gemsets installed at /users/foss/gems


## Separate Gemset to each project
Whenever you change to a directory (cd directory), RVM checks if there is a .rvmrc inside it.
This file is a shell script that can contain any code needed to initialize the project environment.
For example, if you want to load a gemset with a specific ruby version every time you enter the project directory, you just need to create a .rvmrc with the content:

{% highlight ruby %}
$rvm ruby_version@gemset_name
{% endhighlight %}

With that in mind, I standardized the setup of my projects to:

{% highlight ruby %}
$mkdir project_name
$cd project_name
$rvm --create --rvmrc use ruby_version@project_name
{% endhighlight %}

The last command will create a gemset for the project, and also the .rvmrc to load the correct gemset.
As I am also using bundler, I create a Gemfile, listing the dependencies of the project, (even in older projects, with Rails 1.2.6). Then just install them with:

{% highlight ruby %}
$bundle install
{% endhighlight %}

So, I have an isolated gems environment for each project, and the best, every time I switch to a project directory, RVM will already load the correct gemset based on .rvmrc!


## Other rvm commands

To  have a list of the available commands:
{% highlight ruby %}
$rvm help
{% endhighlight %}

To get help on a specific command:
{% highlight ruby %}
$rvm help command
{% endhighlight %}

If you want to uninstall a version of Ruby:
{% highlight ruby %}
$rvm uninstall ruby_version
{% endhighlight %}

To delete a gemset:
{% highlight ruby %}
$rvm gemset use gemset_name
$rvm gemset delete gemset_name
{% endhighlight %}

Check the RVM website for a detailed (and good) documentation at [https://rvm.io/](https://rvm.io/)
