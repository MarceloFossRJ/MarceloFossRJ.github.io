---
layout: post
title:  "Debugging Rails applications"
date:   2017-10-18 19:45:31 -0300
categories: [recipe]
tags: [ror, gems, debug, assert]
author: Marcelo Foss
---
From wikipedia:
*"Debugging is the process of finding and resolving of defects or problem within the program that prevent correct operation of computer software or a system.

Debugging tactics can involve interactive debugging, control flow analysis, unit testing, integration testing, log file analysis, monitoring at the application or system level, memory dumps, and profiling."*

When resolving an issues, there different actions that can be done to find out what is happening, and fix it.
The use of a debugger and logging are the more effective ones.
The standard debugger for Rails is Byebug, and is included in Rails 5 project by default.
The current post is about using a tradutional debugger, Byebug receives most of the focus
You can check the post for logging at []()

https://www.sitepoint.com/the-ins-and-outs-of-debugging-ruby-with-byebug/
http://nofail.de/2013/10/debugging-rails-applications-in-development/

# Using Byebug to debug
[https://rubyplus.com/articles/3631-Debugging-using-ByeBug-Gem-in-Rails-5][https://rubyplus.com/articles/3631-Debugging-using-ByeBug-Gem-in-Rails-5]
[https://fleeblewidget.co.uk/2014/05/byebug-cheatsheet/][https://fleeblewidget.co.uk/2014/05/byebug-cheatsheet/]
When your code is behaving in unexpected ways, you can try printing to logs or the console to diagnose the problem. Unfortunately, there are times when this sort of error tracking is not effective in finding the root cause of a problem. When you actually need to journey into your running source code, the debugger is your best companion.

## Setup
You can use the byebug gem to set breakpoints and step through live code in Rails. To install it, just run:

```ruby
$ gem install byebug
```

Inside any Rails application you can then invoke the debugger by calling the byebug method.  
Here's an example:
```ruby
class PeopleController < ApplicationController
  def new
    byebug
    @person = Person.new
  end
end
```

## The Shell
As soon as your application calls the byebug method, the debugger will be started in a debugger shell inside the terminal window where you launched your application server, and you will be placed at the debugger's prompt (byebug). Before the prompt, the code around the line that is about to be run will be displayed and the current line will be marked by '=>', like this:

```ruby
[1, 10] in /PathTo/project/app/controllers/articles_controller.rb
    3:
    4:   # GET /articles
    5:   # GET /articles.json
    6:   def index
    7:     byebug
=>  8:     @articles = Article.find_recent
    9:
   10:     respond_to do |format|
   11:       format.html # index.html.erb
   12:       format.json { render json: @articles }

(byebug)
```

If you got there by a browser request, the browser tab containing the request will be hung until the debugger has finished and the trace has finished processing the entire request.
Now it's time to explore your application. A good place to start is by asking the debugger for help. Type: help

To see the previous ten lines you should type list- (or l-).

This way you can move inside the file and see the code above the line where you added the byebug call. Finally, to see where you are in the code again you can type list=

## Context
When you start debugging your application, you will be placed in different contexts as you go through the different parts of the stack.

The debugger creates a context when a stopping point or an event is reached. The context has information about the suspended program which enables the debugger to inspect the frame stack, evaluate variables from the perspective of the debugged program, and know the place where the debugged program is stopped.

At any time you can call the backtrace command (or its alias where) to print the backtrace of the application. This can be very helpful to know how you got where you are. If you ever wondered about how you got somewhere in your code, then backtrace will supply the answer.

```ruby
(byebug) where
--> #0  ArticlesController.index
      at /PathToProject/app/controllers/articles_controller.rb:8
    #1  ActionController::BasicImplicitRender.send_action(method#String, *args#Array)
      at /PathToGems/actionpack-5.1.0/lib/action_controller/metal/basic_implicit_render.rb:4
    #2  AbstractController::Base.process_action(action#NilClass, *args#Array)
      at /PathToGems/actionpack-5.1.0/lib/abstract_controller/base.rb:181
    #3  ActionController::Rendering.process_action(action, *args)
      at /PathToGems/actionpack-5.1.0/lib/action_controller/metal/rendering.rb:30
...
```

The current frame is marked with -->. You can move anywhere you want in this trace (thus changing the context) by using the frame n command, where n is the specified frame number. If you do that, byebug will display your new context.
```ruby
(byebug) frame 2

[176, 185] in /PathToGems/actionpack-5.1.0/lib/abstract_controller/base.rb
   176:       # is the intended way to override action dispatching.
   177:       #
   178:       # Notice that the first argument is the method to be dispatched
   179:       # which is *not* necessarily the same as the action name.
   180:       def process_action(method_name, *args)
=> 181:         send_action(method_name, *args)
   182:       end
   183:
   184:       # Actually call the method associated with the action. Override
   185:       # this method if you wish to change how action methods are called,
(byebug)
```

The available variables are the same as if you were running the code line by line. After all, that's what debugging is.

You can also use up [n] and down [n] commands in order to change the context n frames up or down the stack respectively. n defaults to one. Up in this case is towards higher-numbered stack frames, and down is towards lower-numbered stack frames.

## Threads
The debugger can list, stop, resume and switch between running threads by using the thread command (or the abbreviated th). This command has a handful of options:

thread: shows the current thread.
thread list: is used to list all threads and their statuses. The current thread is marked with a plus (+) sign.
thread stop n: stops thread n.
thread resume n: resumes thread n.
thread switch n: switches the current thread context to n.
This command is very helpful when you are debugging concurrent threads and need to verify that there are no race conditions in your code.

## Inspecting Variables
Any expression can be evaluated in the current context. To evaluate an expression, just type it!

This example shows how you can print the instance variables defined within the current context:
```ruby
[3, 12] in /PathTo/project/app/controllers/articles_controller.rb
    3:
    4:   # GET /articles
    5:   # GET /articles.json
    6:   def index
    7:     byebug
=>  8:     @articles = Article.find_recent
    9:
   10:     respond_to do |format|
   11:       format.html # index.html.erb
   12:       format.json { render json: @articles }

(byebug) instance_variables
[:@_action_has_layout, :@_routes, :@_request, :@_response, :@_lookup_context,
 :@_action_name, :@_response_body, :@marked_for_same_origin_verification,
 :@_config]
```

As you may have figured out, all of the variables that you can access from a controller are displayed. This list is dynamically updated as you execute code. For example, run the next line using next
```ruby
(byebug) next

[5, 14] in /PathTo/project/app/controllers/articles_controller.rb
   5     # GET /articles.json
   6     def index
   7       byebug
   8       @articles = Article.find_recent
   9
=> 10       respond_to do |format|
   11         format.html # index.html.erb
   12        format.json { render json: @articles }
   13      end
   14    end
   15
(byebug)
```

And then ask again for the instance_variables:

```ruby
(byebug) instance_variables
[:@_action_has_layout, :@_routes, :@_request, :@_response, :@_lookup_context,
 :@_action_name, :@_response_body, :@marked_for_same_origin_verification,
 :@_config, :@articles]
```

Now @articles is included in the instance variables, because the line defining it was executed.

You can also step into irb mode with the command irb (of course!). This will start an irb session within the context you invoked it.

The var method is the most convenient way to show variables and their values. Let's have byebug help us with it.

```ruby
(byebug) help var

  [v]ar <subcommand>

  Shows variables and its values


  var all      -- Shows local, global and instance variables of self.
  var args     -- Information about arguments of the current scope
  var const    -- Shows constants of an object.
  var global   -- Shows global variables.
  var instance -- Shows instance variables of self or a specific object.
  var local    -- Shows local variables in current scope.
```

This is a great way to inspect the values of the current context variables. For example, to check that we have no local variables currently defined:
```ruby
(byebug) var local
(byebug)
You can also inspect for an object method this way:

(byebug) var instance Article.new
@_start_transaction_state = {}
@aggregation_cache = {}
@association_cache = {}
@attributes = #<ActiveRecord::AttributeSet:0x007fd0682a9b18 @attributes={"id"=>#<ActiveRecord::Attribute::FromDatabase:0x007fd0682a9a00 @name="id", @value_be...
@destroyed = false
@destroyed_by_association = nil
@marked_for_destruction = false
@new_record = true
@readonly = false
@transaction_state = nil
```

You can also use display to start watching variables. This is a good way of tracking the values of a variable while the execution goes on.
```ruby
(byebug) display @articles
1: @articles = nil
The variables inside the displayed list will be printed with their values after you move in the stack. To stop displaying a variable use undisplay n where n is the variable number (1 in the last example).
```

## Step by Step
Now you should know where you are in the running trace and be able to print the available variables. But let's continue and move on with the application execution.

Use step (abbreviated s) to continue running your program until the next logical stopping point and return control to the debugger. next is similar to step, but while step stops at the next line of code executed, doing just a single step, next moves to the next line without descending inside methods.

For example, consider the following situation:
```ruby
Started GET "/" for 127.0.0.1 at 2014-04-11 13:39:23 +0200
Processing by ArticlesController#index as HTML

[1, 6] in /PathToProject/app/models/article.rb
   1: class Article < ApplicationRecord
   2:   def self.find_recent(limit = 10)
   3:     byebug
=> 4:     where('created_at > ?', 1.week.ago).limit(limit)
   5:   end
   6: end

(byebug)
```

If we use next, we won't go deep inside method calls. Instead, byebug will go to the next line within the same context. In this case, it is the last line of the current method, so byebug will return to the next line of the caller method.
```ruby
(byebug) next
[4, 13] in /PathToProject/app/controllers/articles_controller.rb
    4:   # GET /articles
    5:   # GET /articles.json
    6:   def index
    7:     @articles = Article.find_recent
    8:
=>  9:     respond_to do |format|
   10:       format.html # index.html.erb
   11:       format.json { render json: @articles }
   12:     end
   13:   end

(byebug)
```

If we use step in the same situation, byebug will literally go to the next Ruby instruction to be executed -- in this case, Active Support's week method.

```ruby
(byebug) step

[49, 58] in /PathToGems/activesupport-5.1.0/lib/active_support/core_ext/numeric/time.rb
   49:
   50:   # Returns a Duration instance matching the number of weeks provided.
   51:   #
   52:   #   2.weeks # => 14 days
   53:   def weeks
=> 54:     ActiveSupport::Duration.weeks(self)
   55:   end
   56:   alias :week :weeks
   57:
   58:   # Returns a Duration instance matching the number of fortnights provided.
(byebug)
```

This is one of the best ways to find bugs in your code.

You can also use step n or next n to move forward n steps at once.

## Breakpoints
A breakpoint makes your application stop whenever a certain point in the program is reached. The debugger shell is invoked in that line.

You can add breakpoints dynamically with the command break (or just b). There are 3 possible ways of adding breakpoints manually:

* break n: set breakpoint in line number n in the current source file.  

* break file:n [if expression]: set breakpoint in line number n inside file named file. If an expression is given it must evaluated to true to fire up the debugger.

* break class(.|\#)method [if expression]: set breakpoint in method (. and # for class and instance method respectively) defined in class. The expression works the same way as with file:n.

For example, in the previous situation
```ruby
[4, 13] in /PathToProject/app/controllers/articles_controller.rb
    4:   # GET /articles
    5:   # GET /articles.json
    6:   def index
    7:     @articles = Article.find_recent
    8:
=>  9:     respond_to do |format|
   10:       format.html # index.html.erb
   11:       format.json { render json: @articles }
   12:     end
   13:   end

(byebug) break 11
Successfully created breakpoint with id 1
```

Use info breakpoints to list breakpoints. If you supply a number, it lists that breakpoint. Otherwise it lists all breakpoints.

```ruby
(byebug) info breakpoints
Num Enb What
1   y   at /PathToProject/app/controllers/articles_controller.rb:11
```

To delete breakpoints: use the command delete n to remove the breakpoint number n. If no number is specified, it deletes all breakpoints that are currently active.

```ruby
(byebug) delete 1
(byebug) info breakpoints
No breakpoints.
```

You can also enable or disable breakpoints:

* enable breakpoints [n [m [...]]]: allows a specific breakpoint list or all breakpoints to stop your program. This is the default state when you create a breakpoint.
* disable breakpoints [n [m [...]]]: make certain (or all) breakpoints have no effect on your program.

## Catching Exceptions
The command catch exception-name (or just cat exception-name) can be used to intercept an exception of type exception-name when there would otherwise be no handler for it.

To list all active catchpoints use catch.

## Resuming Execution
There are two ways to resume execution of an application that is stopped in the debugger:

* continue [n]: resumes program execution at the address where your script last stopped; any breakpoints set at that address are bypassed. The optional argument n allows you to specify a line number to set a one-time breakpoint which is deleted when that breakpoint is reached.

* finish [n]: execute until the selected stack frame returns. If no frame number is given, the application will run until the currently selected frame returns. The currently selected frame starts out the most-recent frame or 0 if no frame positioning (e.g up, down or frame) has been performed.   If a frame number is given it will run until the specified frame returns.

## Editing
Two commands allow you to open code from the debugger into an editor:

edit [file:n]: edit file named file using the editor specified by the EDITOR environment variable. A specific line n can also be given.

## Quitting
To exit the debugger, use the quit command (abbreviated to q). Or, type q! to bypass the Really quit? (y/n) prompt and exit unconditionally.

A simple quit tries to terminate all threads in effect. Therefore your server will be stopped and you will have to start it again.

# View Helpers for debugging your application
## debug
Returns a '<pre' tag that renders the object using the YAML format. This will generate human-readable data from any object.

## to_yaml
Calling to_yaml on any object converts it to YAML. You can pass this converted object into the simple_format helper method to format the output.

## inspect
Prints the object value as a string.

# Debugging with the web-console gem
Web Console is a bit like byebug, but it runs in the browser. In any page you are developing, you can request a console in the context of a view or a controller. The console would be rendered next to your HTML content.

## Console
Inside any controller action or view, you can invoke the console by calling the console method.

For example, in a controller:
```ruby
class PostsController < ApplicationController
  def new
    console
    @post = Post.new
  end
end
```

Or in a view:
```ruby
<% console %>

<h2>New Post</h2>
```

This will render a console inside your view. You don't need to care about the location of the console call; it won't be rendered on the spot of its invocation but next to your HTML content.

The console executes pure Ruby code: You can define and instantiate custom classes, create new models and inspect variables.

Only one console can be rendered per request. Otherwise web-console will raise an error on the second console invocation.

## Inspecting Variables
You can invoke instance_variables to list all the instance variables available in your context. If you want to list all the local variables, you can do that with local_variables.

## Settings
config.web_console.whitelisted_ips: Authorized list of IPv4 or IPv6 addresses and networks (defaults: 127.0.0.1/8, ::1).
config.web_console.whiny_requests: Log a message when a console rendering is prevented (defaults: true).
Since web-console evaluates plain Ruby code remotely on the server, don't try to use it in production.

# Plugins for Debugging
There are some Rails plugins to help you to find errors and debug your application. Here is a list of useful plugins for debugging:

* Footnotes: Every Rails page has footnotes that give request information and link back to your source via TextMate.

* Query Trace: Adds query origin tracing to your logs.

* Query Reviewer: This Rails plugin not only runs "EXPLAIN" before each of your select queries in development, but provides a small DIV in the rendered output of each page with the summary of warnings for each query that it analyzed.

* Exception Notifier: Provides a mailer object and a default set of templates for sending email notifications when errors occur in a Rails application.

* Better Errors: Replaces the standard Rails error page with a new one containing more contextual information, like source code and variable inspection.

* RailsPanel: Chrome extension for Rails development that will end your tailing of development.log. Have all information about your Rails app requests in the browser — in the Developer Tools panel. Provides insight to db/rendering/total times, parameter list, rendered views and more.

* Pry: An IRB alternative and runtime developer console.

# References
byebug Homepage
web-console Homepage

https://github.com/pry/pry
