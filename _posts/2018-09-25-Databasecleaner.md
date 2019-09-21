---
layout: post
title: DatabaseCleaner strategy with RSpec, Capybara and Selenium
date:   2018-09-25 18:44:25 -0300
category: [recipe ]
tags: [rspec, capybara, databasecleaner, cheatsheet, test, testing, tdd, bdd]
author: Marcelo Foss
intro: |
  [DatabaseCleaner](https://github.com/DatabaseCleaner/database_cleaner){:target="\_blank"} is a set of gems containing strategies for cleaning your database in Ruby. The original scenario was to ensure a clean state during tests. Each strategy is a small amount of code but is code that is usually needed in any ruby app that is testing with a database.
---

## Spec_helper
First disable rspec-rails implicit wrapping of tests in a database transaction at `spec_helper.rb` :

```ruby
config.use_transactional_fixtures = false
```

## Database_cleaner strategy
Create a file to hold database_cleaner configurationa:  `spec/support/database_cleaner.rb`:

```ruby
#spec/support/database_cleaner.rb
RSpec.configure do |config|

#Clear the test database completely before running all tests.   
  config.before(:suite) do
    DatabaseCleaner.clean_with(:truncation)
  end

#Sets the default database cleaning strategy to be transactions.
  config.before(:each) do
    DatabaseCleaner.strategy = :transaction
  end

#This line only runs before examples which have been flagged :js => true. Where the transaction setting won’t work, so this code overrides the setting and chooses the “truncation” strategy instead.
  config.before(:each, :js => true) do
    DatabaseCleaner.strategy = :truncation
  end

#These lines hook up database_cleaner around the beginning and end of each test, telling it to execute whatever cleanup strategy we selected beforehand.
  config.before(:each) do
    DatabaseCleaner.start
  end

  config.after(:each) do
    DatabaseCleaner.clean
  end

end
```
