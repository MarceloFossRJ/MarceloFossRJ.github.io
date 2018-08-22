---
layout: post
title:  "Logging Rails applications"
date:   2017-10-09 19:45:31 -0300
categories: [article]
tags: [ror, gems, log, logging, debug, assert]
author: Marcelo Foss
---

# Logger
It can also be useful to save information to log files at runtime. Rails maintains a separate log file for each runtime environment.
By default, each log is created under Rails.root/log/ and the log file is named after the environment in which the application is running.

Rails makes use of the ActiveSupport::Logger class to write log information. Other loggers, such as  the Log4r gem, can be used and substitute it.
The alternative logger can be specified in config/application.rb or any other environment file, for example:

```ruby
# config/application.rb
config.logger = Logger.new(STDOUT)
config.logger = Log4r::Logger.new("Application Log")
```

Or in the Initializer section, add any of the following

```ruby
#
Rails.logger = Logger.new(STDOUT)
Rails.logger = Log4r::Logger.new("Application Log")
```

## Log levels
When something is logged, it's printed into the corresponding log if the log level of the message is equal to or higher than the configured log level. If you want to know the current log level, you can call the Rails.logger.level method.

The available log levels are: :debug, :info, :warn, :error, :fatal, and :unknown, corresponding to the log level numbers from 0 up to 5, respectively.
To change the default log level, use:

```ruby
config.log_level = :warn # In any environment initializer, or
Rails.logger.level = 0 # at any time
```
The default Rails log level is debug in all environments.

## Writing your log messages
To write in the current log use the logger.(debug|info|warn|error|fatal) method from within a controller, model or mailer:

```ruby
logger.debug "Person attributes hash: #{@person.attributes.inspect}"
logger.info "Processing the request..."
logger.fatal "Terminating application, raised unrecoverable error!!!"
```
## Tagged logging
When running multi-user, multi-account applications, it's often useful to be able to filter the logs using some custom rules. TaggedLogging in Active Support helps you do exactly that by stamping log lines with subdomains, request ids, and anything else to aid debugging such applications.

```ruby
logger = ActiveSupport::TaggedLogging.new(Logger.new(STDOUT))
logger.tagged("BCX") { logger.info "Stuff" }                            # Logs "[BCX] Stuff"
logger.tagged("BCX", "Jason") { logger.info "Stuff" }                   # Logs "[BCX] [Jason] Stuff"
logger.tagged("BCX") { logger.tagged("Jason") { logger.info "Stuff" } } # Logs "[BCX] [Jason] Stuff"
```

## Logs & Performance
Logging will always have a small impact on the performance of your Rails app, particularly when logging to disk. Additionally, there are a few subtleties:

Using the :debug level will have a greater performance penalty than :fatal, as a far greater number of strings are being evaluated and written to the log output (e.g. disk).

Another potential pitfall is too many calls to Logger in your code:

```ruby
logger.debug "Person attributes hash: #{@person.attributes.inspect}"
```

In the above example, there will be a performance impact even if the allowed output level doesn't include debug. The reason is that Ruby has to evaluate these strings, which includes instantiating the somewhat heavy String object and interpolating the variables. Therefore, it's recommended to pass blocks to the logger methods, as these are only evaluated if the output level is the same as — or included in — the allowed level (i.e. lazy loading). The same code rewritten would be:

```ruby
logger.debug {"Person attributes hash: #{@person.attributes.inspect}"}
```

The contents of the block, and therefore the string interpolation, are only evaluated if debug is enabled. This performance savings are only really noticeable with large amounts of logging, but it's a good practice to employ.


Link to setup Loggly [link]
[https://www.sitepoint.com/optimizing-ruby-logging-faster-debugging-problem-solving/]

Tools for production logging [link]
[https://www.reddit.com/r/rails/comments/5u1lzn/rails_production_logging_in_2017/]


# Logging Commercial Solutions
https://logz.io/product/
https://logentries.com/
https://www.sumologic.com/pricing/
https://www.loggly.com/
https://www.elastic.co/products/logstash

# Good logging reads
https://lequangkhai.wordpress.com/2014/10/30/strategies-for-rails-logging-and-error-handling/

http://www.railsonmaui.com/blog/2013/05/08/strategies-for-rails-logging-and-error-handling/
