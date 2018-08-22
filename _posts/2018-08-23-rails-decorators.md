---
layout: post
title:  "Decorator pattern in Rails engines"
date:   2018-08-23 18:44:25 -0300
categories: [recipe]
tags: [rails, engines, patterns, decorator]
keywords:
  - "Ror engines"
  - "rails engines decorator"
  - "first_name 'John'"
  - "sequence(:username) { |n| \"user#{n}\" }"
author: Marcelo Foss
intro: |
  No matter if you are writing a component based application in RoR, or if you are just using a gem in your project, there is a big probability that you are going to need to extend some gem/engine classes. 
---

No matter if you are writing a component based application in RoR, or if you are just using a gem in your project, there is a big probability that you are going to need to extend some gem/engine classes.  
That's what we want to handle here.

From wikipedia:
[Decorator Pattern](https://en.wikipedia.org/wiki/Decorator_pattern)  
*In object-oriented programming, the decorator pattern is a design pattern that allows behavior to be added to an individual object, dynamically, without affecting the behavior of other objects from the same class. The decorator pattern is often useful for adhering to the Single Responsibility Principle, as it allows functionality to be divided between classes with unique areas of concern.The decorator pattern is structurally nearly identical to the chain of responsibility pattern, the difference being that in a chain of responsibility, exactly one of the classes handles the request, while for the decorator, all classes handle the request.*

So lets stop with talk and go directly to the code itself.

1- First step
Create a directories to save the your decorator files. I do recommend to do it inside the app directory:
```
mkdir \app\decorators\models
mkdir \app\decorators\controllers
```

2- Second step
create an initializer so rails can load the files in the dirctories just created.  

```ruby
#/config/initializers/decorators.rb
files = Dir.glob("#{Rails.root}/app/decorators/**/*.rb")

files.each do |f|
  require f
end
```

3- Third step
create the file to extend the class you want. The file should have the extension *_decorator.rb*, and the original name of the class, ie *user_decorator.rb*.  

```ruby
#/app/decorators/model/user_decorator.rb
AcaRails::User.class_eval do

  after_create :create_client

  private
  def create_client
    c = Base::Client.new
    c.subdomain = login.downcase
    c.save!
  end
end
```

and thats all!
