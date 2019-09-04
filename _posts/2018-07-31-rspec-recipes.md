---
layout: post
title:  "RSpec recipes"
date:   2018-07-31 09:15:54 -0300
categories: [recipe]
tags: [ruby, ror, test, bdd, tdd, rspec, rails]
author: Marcelo Foss
---

RSpec is a hugely popular behavior driven development (BDD) oriented testing framework in the Ruby community. It is the my choice for testing framework, but I will not delve on the reasoning of using it now, this is maybe a discussion for a future post.

Another tool that is a kind of sibling to RSpec, is Capybara:
_Capybara helps you test web applications by simulating how a real user would interact with your app. It is agnostic about the driver running your tests and comes with Rack::Test and Selenium support built in. WebKit is supported through an external gem._

Database Cleaner
_Database Cleaner is a set of strategies for cleaning your database in Ruby.
The original use case was to ensure a clean state during tests. Each strategy is a small amount of code but is code that is usually needed in any ruby app that is testing with a database._

# Configuring RSpec

Add the gems to your gemfile:

```ruby
group :development, :test do
  gem "database_cleaner"
  gem "rspec-rails"
end
group :test do
  gem "capybara"
end
```

Execute the following command;
```
$ rails generate rspec:install
```
which generates the files:  
.rspec  
spec/spec_helper.rb  
spec/rails_helper.rb

the ```spec/spec_helper.rb``` file is for specs that do not on Rails (such as specs for classes in the lib directory).  
The ```spec/rails_helper.rb``` file is for specs that depend on Rails (in a Rails project, most or all of them, like models, controllers). rails_helper.rb requires spec_helper.rb. So in a Rails project preferably require it (and not spec_helper.rb) in your specs.  
If you want your non-Rails-dependent specs to enforce that they're non-Rails-dependent, and to run as fast as possible when you run them by themselves, you could require spec_helper.rb rather than rails_helper.rb in those. But it's very convenient to -r rails_helper in your .rspec rather than requiring one helper or the other in each spec file, so that is sure to be a popular approach.  
If you're using the spring preloader, each class only needs to be loaded once, and spring loads classes eagerly even if you only run a single spec that requires spec_helper, so there isn't as much value in requiring only spec_helper in some files.

## spec_helper.rb config

```ruby
# spec/spec_helper.rb
RSpec.configure do |config|
  config.expect_with :rspec do |expectations|
    expectations.include_chain_clauses_in_custom_matcher_descriptions = true
  end
  config.mock_with :rspec do |mocks|
    mocks.verify_partial_doubles = true
  end
  config.shared_context_metadata_behavior = :apply_to_host_groups
  config.filter_run_when_matching :focus
  config.example_status_persistence_file_path = "spec/examples.txt"
  config.disable_monkey_patching!
  config.default_formatter = 'doc' if config.files_to_run.one?
  config.order = :random
  Kernel.srand config.seed
  # config.profile_examples = 10
end
```
mocks.verify_partial_doubles = true prevents you from stubbing any methods that don’t already exist on an object. In other words, it helps typo-proof your mocks.

config.disable_monkey_patching! prevents RSpec from monkey patching should and stub onto pretty much everything. This makes your objects behave in your tests as they would in “real” life.

config.order = :random and Kernel.srand config.seed make sure that the tests are run in a random order each time they’re run. This helps keep each test independent of one another by making sure they can’t accidentally depend on each other’s side effects.

I’ve also left config.profile_examples commented out because while it is really helpful to have it run to identify tests that are slowing your suite down, the additional console output from it can get a bit noisy.

## rails_helper.rb

```ruby
ENV["RAILS_ENV"] ||= "test"
require File.expand_path("../../config/environment", __FILE__)
abort("The Rails environment is running in production mode!") if Rails.env.production?
require "spec_helper"
require "rspec/rails"
# Add additional requires below this line. Rails is not loaded until this point!
# Dir[Rails.root.join("spec/support/**/*.rb")].each { |f| require f }
ActiveRecord::Migration.maintain_test_schema!
RSpec.configure do |config|
  # Remove this line if you're not using ActiveRecord or ActiveRecord fixtures
  config.fixture_path = "#{::Rails.root}/spec/fixtures"
  config.use_transactional_fixtures = true
  config.infer_spec_type_from_file_location!
  config.filter_rails_from_backtrace!
end
```

The only default to change is config.use_transactional_fixtures from true to false. This stops RSpec Rails from wrapping the tests in database transactions which means that, records created for one test will be visible to the next test.

## Capybara

 To add Capybara to RSpec simply add
 ```ruby
 #rails_helper.rb
 require "capybara/rspec"
 ```
 to the list of requires in the rails_helper.rb file.


## database_cleaner


First, start by making sure our tests start with a clean slate by adding the following to our RSpec.configure block:
```ruby
config.before(:suite) do
  DatabaseCleaner.clean_with(:truncation)
end
```

Now we need to tell Database Cleaner how to manage the database for regular tests
```ruby
config.before(:each) do
  DatabaseCleaner.strategy = :transaction
end
config.before(:each, js: true) do
  DatabaseCleaner.strategy = :truncation
end
```

The order of these blocks is very significant. Right now our js: true block overrides the plain one when a spec is js: true. If it were reversed, Database Cleaner would always be using transactions and since our js: true tests can’t see those records there would be problems. Next we have to actually clean up the database:

```ruby
# This block must be here, do not combine with the other `before(:each)` block.
# This makes it so Capybara can see the database.
config.before(:each) do
  DatabaseCleaner.start
end
config.after(:each) do
  DatabaseCleaner.clean
end
```

You may want to combine the before(:each) we just added with the previous one but don’t. If you combine them, Database Cleaner will open a transaction before being set to use truncation, which means Capybara won’t be able to “see” the test database records.


# Good reads

https://www.devmynd.com/blog/setting-up-rspec-and-capybara-in-rails-5-for-testing/

https://github.com/rspec/rspec-rails

https://github.com/CodingItWrong/rails-rspec-capybara-javascript

https://robots.thoughtbot.com/rspec-integration-tests-with-capybara

https://medium.com/@ethanryan/testing-your-app-in-the-browser-with-capybara-rails-backend-react-frontend-e409671c4596
