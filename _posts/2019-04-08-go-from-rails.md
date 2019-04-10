---
layout: post
title:  "Calling Golang script from a Rails app"
date:   2019-04-02 19:45:31 -0300
categories: [recipe]
tags: [database, document, nosql, mongodb, rails, ruby, RoR, db]
author: Marcelo Foss
---
## First step: create a Rake Task
Let’s start with a simple rake task, at the terminal execute the following command:
```
$ rails generate task CallGoRoutine
```
We used the Rails generator script to create the task, which creates the task file at `lib/tasks/call_go_routine.rake`.

```ruby
# call_go_routine.rake
namespace :call_go_routine do
end
```
Let's add some code in order to have it calling our script.   
First we create a folder inside `/lib` to be the host of our personalized scripts. I will call it `scripts`:
```
$ mkdir \lib\scripts
```
Now you can copy your go routine inside this folder. For that example is a simple `main` that is already compiled.
Let's add some code to have the rake task calling the go routine
```ruby
# lib\tasks\call_go_routine.rake
namespace :call_go_routine do
  desc "Runs an external Golang script"
  task :run_go => :environment do
      puts "running  a Go routine!"
      filepath = Rails.root.join('lib', 'scripts', 'main')
      output = ` #{filepath}`
  end
end
```

## Adding parameters

### Using Using ARGV
That is my preference when we need few arguments.

When you run a rake task, the tasks names are bundled up a constant – ARGV. So it's possible to:
```ruby
  # lib\tasks\call_go_routine.rake
  namespace :call_go_routine do
    desc "Runs an external Golang script"
    task :run_go => :environment do
      puts "running Go!"
      ARGV.each { |a| task a.to_sym do ; end }
      filepath = Rails.root.join('lib', 'scripts', 'main')
      output = ` #{filepath} #{ARGV[1].to_s}`
      puts output
    end
  end
  ```
And this would be run like this:
```
$ rake call_go_routine:run_go param
```

### Using [] - the Rake way
Rake has a built-in way of accepting arguments:
```ruby
# lib\tasks\call_go_routine.rake
namespace :call_go_routine do
  desc "Runs an external Golang script"
  task :run_go, [:param1, :param2] do |t, args|
      puts "running  a Go routine!"
      filepath = Rails.root.join('lib', 'scripts', 'main')
      output = ` #{filepath} #{args[:param1].to_s} #{args[:param2].to_s}`
  end
end
```
And this would be run like this:
```
$ rake add[1,2]
```
I am not a fan of this method since use the brackets for the parameters input, which is not really a standard. User's of zsh shell also reported errors, and have to escape the brackets to have it working.

### Using environmental variables

Another option is to use environment variables. It's very simple:
```ruby
# lib\tasks\call_go_routine.rake
namespace :call_go_routine do
  desc "Runs an external Golang script"
  task :run_go => :environment do
      puts "running  a Go routine!"
      filepath = Rails.root.join('lib', 'scripts', 'main')
      output = ` #{filepath} #{ENV['param1'].to_s}`
  end
end

```
Running this would look like:
```
$ rake add param1=1
```
The caveat here is that the usage of environmental variables forces you to type in caps.

## Calling from a rails Model
Calling directly a Golang routine from a model, is not really the more recommended approach. I would recommend to encapsule your Go routine in an webservice and then have the rails app consume it, but maybe there is an occasion might need it

```Ruby
require 'rake'
MongoPsql::Application.load_tasks

class User < ApplicationRecord

  after_create do
    User.run_rake('call_go_routine:run_go', 'p')
  end

    def self.run_rake(task_name, param)
      load File.join(Rails.root, 'lib', 'tasks', 'my_task_to_call_go_routine.rake')
      Rake::Task['call_go_routine:run_go'].execute(OpenStruct.new({param1: param}))
    end
end
```

### Interesting reference about Rake
[https://medium.com/@sampatbadhe/rake-task-invoke-or-execute-419cd689c3bd](https://medium.com/@sampatbadhe/rake-task-invoke-or-execute-419cd689c3bd)

[https://www.stuartellis.name/articles/rake/](https://www.stuartellis.name/articles/rake/)

[http://tutorials.jumpstartlab.com/topics/systems/automation.html](http://tutorials.jumpstartlab.com/topics/systems/automation.html)
