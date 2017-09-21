---
layout: post
title:  "Engine parameters"
date:   2017-07-10 19:45:31 -0300
categories: [recipes]
tags: [ruby]
author: Marcelo Foss
---
I have a mountable engine called Blog that apps can use.
What's the best way to allow apps that use the engine to set a configuration variable like site_name (so that the engine can display it in the design)?
Update:
I've seen some gems create a 'config/initializers/gem_name.rb' file. Is there any specification on how to:
	1. Create a file like that on the engine's side
	2. Copy it into the app's side
	3. How to access those set variables on the engine's side?
I tried creating Blog.site_name = "My Site" in the app's config/initializers/blog.rb file but get an Undefined method error.


Figured out an even better solution that also allows you to set default values (incase the app using the engine doesn't specify a config)...
1) Create config variables in your app's /config/initializers/blog.rb like this:
Blog.setup do |config|
    config.site_name = "My Site Name"
end
2) In your engine's /lib/blog/engine.rb set default values like this:
module Blog
class << self
        mattr_accessor :site_name
        self.site_name = "Site Name"
# add default values of more config vars here
    end
# this function maps the vars from your app into your engine
     def self.setup(&block)
        yield self
     end
end
3) Now you can simply access the config variables in your engine like this:
Blog.site_name
Much cleaner.


After a lot of testing and looking into existing gems, here is what works in Rails 4:
Considering your engine's name is Blog:
In your engine's /lib/blog/engine.rb put in this:
module Blog
def self.setup(&block)
        @config ||= Blog::Engine::Configuration.new
yield @config if block
@config
    end
def self.config
        Rails.application.config
    end
end
In your app, create a file called /config/initalizers/blog.rb and set config vars like this:
Blog.setup do |config|
    config.testingvar = "asdfasdf"
end
Then you can access these config variables ANYWHERE in your engine like this:
Blog.config.testingvar
Hope this helps someone. There is very little documentation on this right now so it was all trial and error.
