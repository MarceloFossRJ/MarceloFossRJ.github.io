---
layout: post
title:  "Rails engines controllers"
date:   2018-08-24 18:44:25 -0300
categories: [recipe]
tags: [rails, engines, controllers]
keywords:
  - "Ror engines"
  - "rails engines controllers"
author: Marcelo Foss
intro: |
  [Factory Bot](http://www.rubydoc.info/gems/factory_bot/) is a helper for writing factories for Ruby tests.
  It was previously known as Factory Girl. For older versions, use `FactoryGirl` instead of `FactoryBot`.
---


I am writing a rails engine.

I have two methods(authenticate and current_user) in the gem inside application controller. https://github.com/krunal/aadhar/blob/master/app/controllers/aadhar/application_controller.rb

I want 'authenticate' method should be available as a before_filter in parent rails application.

I want 'current_user' method should be available as a method and helper in parent rails application .

I don't know how can I achieve that.

Let say in my parent application, I have a posts controller. I want to use 'authenticate' and 'current_user' this way.

PostsController < ApplicationController
before_filter :authenticate

def index
  current_user.posts
end

--------------------


You're using isolate_namespace in your engine (recommended) but that means you need to use the namespace within the parent application. So if you change it to:

PostsController < Aadhar::ApplicationController
You can access the authenticate and current_user methods. Keep in mind, you'll have to add

helper_method :current_user
in the controller to be able to access it from the view. I tried this and can confirm it works.
