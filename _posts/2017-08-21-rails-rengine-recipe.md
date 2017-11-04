---
layout: post
title:  "Rails Engines Recipes"
date:   2017-08-21 11:13:20 -0300
categories: [recipe]
tags: [rails, engines, gem, components, rspec, cucumber, capybara, shoulda, factory-girl]
author: Marcelo Foss
---
Setting up a rails 5 engine with Rspec, Cucumber, Capybara, Shoulda and Factory_girl

### Initial Steps
#### Configure RVM
```
$rvm --create --rvmrc use ruby_version@project_name
```
#### Install Rails
```
$gem install rails -v 5.0.4
```
#### Create the engine
```
$ rails plugin new <<GemName>> --mountable -T --dummy-path=spec/dummy
```

* -T tells the generator to skip Test::Unit
* --mountable tells the generator that you want a namespaced engine
* --full tells the generator that you want app and config directories
* --dummy-path is the rails app that is generated for your tests. It's called test/dummy by default, but I never liked calling it that.

#### Git repository
Initiate an empty repository in local machine, at the project directory:  
```
$git init
```
create the readme file
```
$touch README.md
```
add database.yml and secrets.yml to your .gitginore
```ruby
#.gitignore
spec/dummy/config/database.yml
spec/dummy/config/secrets.yml
.rvmrc
.idea/
```

Add files to rep
```
$git add .
```
Commit all changes to local repo  
```
$git commit -m 'first commit'
```

#### Push changes to GitHub
Create the repository at github.com  
To create a remote named origin pointing at github repo
```
$git remote add origin https://github.com/marcelofossrj/b109.git
```
Sends the commits in the master branch at github
```
$git push origin master
```


### Configure the Gem to handle migrations
The default behavior of the engine is to copy all migrations found in the engine/db/migrate folder in the target app. It is a better approach that the migrations have their date and name updated in the target application so it do not generate conflicts in the target application migrations.
Edit the engine.rb file:


{% highlight ruby %}
#lib/<<GemName>>/engine.rb
module GemName
   class Engine < Rails::Engine
     isolate_namespace GemName
     initializer :append_migrations do |app|
       unless app.root.to_s.match root.to_s+File::SEPARATOR
         app.config.paths["db/migrate"].concat config.paths["db/migrate"].expanded
       end
     end
   end
end
{% endhighlight %}
Now rake db:migrate will find and correctly run the engine’s migrations.

### Configure the gemspec file
Open the <<GemName>>.gemspec file and replace all the TODO text with the proper information related to the gem specification.  

### Configure the test frameworks
Add the reference to Rspec, Capybara and Factory_girl to the gemspec file
```ruby
#<<GemName>>.gemspec
s.add_dependency "rspec-rails", "~> 3.6.0"  
s.add_dependency "capybara", "~> 2.14.4"  
s.add_dependency "factory_girl_rails", "~> 4.8.0"  
s.add_dependency "shoulda", "~> 3.5.0"  
s.add_dependency "database_cleaner", "~> 1.6.1"  
s.add_dependency "cucumber-rails", "~> 1.5.0"  
```

Edit the Gemfile to add the reference to the gems
```ruby
#Gemfile
group :development, :test do
  gem "rspec-rails", "~> 3.6.0"
  gem "factory_girl_rails", "~> 4.8.0"
  gem "shoulda", "~> 3.5.0"
end
group :test do
  gem "database_cleaner", "~> 1.6.1"
  gem "capybara", "~> 2.14.4"
  gem "cucumber-rails", "~> 1.5.0", require: false
  gem "simplecov", "~> 0.14.1"
end
```

### Install and configure RSpec  
```
$bundle install
$rails generate rspec:install
```

Modify spec/spec_helper.rb to work with the engine file structure:
```ruby
# spec/spec_helper.rb
ENV["RAILS_ENV"] ||= 'test'
require File.expand_path("../dummy/config/environment", __FILE__)
require 'rspec/rails'
require 'rspec/autorun'
require 'factory_girl_rails'
require 'capybara/rails'
require 'capybara/rspec'
require 'shoulda/matchers'
require 'database_cleaner'

Dir[<<GemName>>::Engine.root.join("spec/support/**/*.rb")].sort.each {|f| require f}
ENGINE_RAILS_ROOT=File.join(File.dirname(__FILE__), '../')
Dir[File.join(ENGINE_RAILS_ROOT, "spec/support/**/*.rb")].sort.each {|f| require f }

# Checks for pending migrations before tests are run.  
# If you are not using ActiveRecord, you can remove this line.
ActiveRecord::Migration.check_pending! if defined?(ActiveRecord::Migration)

RSpec.configure do |config|
  config.expect_with :rspec do |expectations|
    expectations.include_chain_clauses_in_custom_matcher_descriptions = true
  end
  config.mock_with :rspec do |mocks|
    mocks.verify_partial_doubles = true
  end

  config.disable_monkey_patching!
  config.warnings = false
  config.profile_examples = nil
  config.order = :random # Run specs in random order to surface order dependencies. If you find
  Kernel.srand config.seed

  #Add the following lines to configure database_cleaner
  config.before(:suite) do
     DatabaseCleaner.strategy = :transaction
     DatabaseCleaner.clean_with(:truncation)
  end

  config.around(:each) do |example|
     DatabaseCleaner.cleaning do
       example.run
     end
  end

  config.shared_context_metadata_behavior = :apply_to_host_groups

  # Add this line to configure factory_girl
  config.include FactoryGirl::Syntax::Methods # Factory_girl config

  # Remove this line if you're not using ActiveRecord or ActiveRecord, fixtures
  config.fixture_path = "#{::Rails.root}/spec/fixtures"

  # If you're not using ActiveRecord, or you'd prefer not to run each of your examples within a    
  # transaction, remove the following line or assign false instead of true.
  config.use_transactional_fixtures = true

  # If true, the base class of anonymous controllers will be inferred automatically.
  # This will be the default behavior in future versions of rspec-rails.
  config.infer_base_class_for_anonymous_controllers = false
End
```

Create the directory where the factories will be created
```
$mkdir spec/factories
```

Create the directories to create the rspec tests
```
$mkdir spec/features
$mkdir spec/support
$mkdir spec/api
$mkdir spec/controllers
$mkdir spec/integration
$mkdir spec/models
$mkdir spec/request
```

Tell the engine that Rspec is the default testing framework:
```ruby
# lib/appointly/engine.rb
module GemName
   class Engine < ::Rails::Engine
      isolate_namespace GemName
      initializer :append_migrations do |app|
         unless app.root.to_s.match root.to_s+File::SEPARATOR
            app.config.paths["db/migrate"].concat config.paths["db/migrate"].expanded
         end
      end
      config.generators do |g|
         g.test_framework :rspec, :fixture => false
         g.fixture_replacement :factory_girl, :dir => 'spec/factories'
      end
   end
End
```

Edit the spec/rails_helper.rb to correct the path for the dummy application and to have RSpec recognizing the rails engine url_helpers
```ruby
# spec/rails_helper.rb
# Replace the line
# require File.expand_path("../../config/environment", __FILE__)
# to
require File.expand_path("../dummy/config/environment", __FILE__) ##


# Add the following line right after
# RSpec.configure do |config|
config.include <<GemName>>::Engine.routes.url_helpers
```

#### Test Rspec installation

Create a scaffold, and run migrations:
```
$ rails g scaffold tenant name:string subdomain:string
$ rm -rf test # For some reason Rails still generates the test directory
$ rake db:create
$ rake db:migrate RAILS_ENV=test
```

Runs Spec tests
```
$bundle exec rake app:db:migrate
$bundle exec rake app:db:test:prepare
```
or
```
$ rspec spec/controllers/gem_name/tenants_controller_spec.rb
```
It will fail...    
What I suppose is that since it is an isolated engine (i.e. an engine designed not to interfere with the routes, helpers, etc. defined in your application).  
It is necessary to explicitly say that the engine routes are being used, not the dummy app's routes.  
Add the command ```use_route: :gem_name``` to your spec, firt to the INDEX action:
```ruby
#spec/controllers/gem_name/tenants_controller_spec.rb
describe "GET index" do
  it "assigns all tenants as @tenants" do
    tenant = Tenant.create! valid_attributes
    # get :index, {}, valid_session
    get :index, { use_route: :gem_name }, valid_session
    assigns(:tenants).should eq([tenant])
  end
end
```
and the same to the NEW action
```ruby
#spec/controllers/gem_name/tenants_controller_spec.rb
describe "GET #new" do
  it "returns a success response" do
    #get :new, params: {}, session: valid_session
    get :new, { use_route: :gem_name }, valid_session
    expect(response).to be_success
  end
end
```
Run the spec again...
```
$rspec spec/controllers/gem_name/tenants_controller_spec.rb
```
...and it will pass.  
Run ```rspec``` for tests or ```spec/dummy/bin/rails s``` for running test app

### Install and configure Cucumber
```
$ rails generate cucumber:install
```
The command will create a features folder under the engine root folder.  
Open the features/support/env.rb file and do the following:
```ruby
# features/support/env.rb
# Add the 3 lines below befor the require cucumber/rails command
ENV["RAILS_ENV"] ||= 'test' ##
require File.expand_path("../../../spec/dummy/config/environment", __FILE__) ##
ENV["RAILS_ROOT"] ||= File.dirname(__FILE__) + '../../../spec/dummy' ##
require 'cucumber/rails'
# Add the following line to use factory_girl
require 'factory_girl_rails'
require File.expand_path(File.dirname(__FILE__) + '/../../spec/factories')
```
