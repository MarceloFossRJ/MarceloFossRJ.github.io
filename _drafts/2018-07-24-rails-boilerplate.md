## Setup a new project and database.
Create a new rails app named *app_name* without Test::Unit and with mysql database:
```console
rails new app_name -T -d mysql
```
or an app without Test::Unit and  with postgresql database:
```console
rails new app_name -T -d postgresql
```
or without Test::Unit and with the default sqlite:
```console
rails new app_name -T
```

####Database credentials from environment variables.
If you are using mysql or postgres, username and password must be filled in `config/database.yml`.
Instead of constants, use environment variables:
```yaml
username: <%= ENV["DEVELOPMENT_DATABASE_USERNAME"] %>
password: <%= ENV["DEVELOPMENT_DATABASE_PASSWORD"] %>
```

Add dotenv in gemfile:
```ruby
group :development, :test do
  gem 'dotenv-rails'
end
```
And install it:
```
bundle install
```

Create a new file in root `.env` (which we will exclude from version control) and export the environment variables:
```console
export DEVELOPMENT_DATABASE_USERNAME=your_username
export DEVELOPMENT_DATABASE_PASSWORD=your_password
```

Create and migrate the database:
```console
rake db:create
rake db:migrate
```

## Rspec testing framework
Add to the gemfile (in development and test groups):
```ruby
group :development, :test do
  gem 'rspec-rails'
  gem 'factory_girl_rails'
end

group :test do
  gem "faker"
  gem "capybara"
  gem "database_cleaner"
  gem "launchy"
  gem "selenium-webdriver"
end
```

Download and install by running:
```
bundle install
```

Initialize the `spec/` directory (where specs will reside) with:
```
rails generate rspec:install
```

Change the rspec format to doxumentation by adding the following line in `.rspec` file:
```ruby
--format documentation
```

Change some testing options and use factory-girl instead of fixtures by adding the following in `config/application.rb` (inside Application class):
```ruby
config.generators do |g|
  g.test_framework :rspec,
    fixtures: true,
    view_specs: false,
    helper_specs: false,
    routing_specs: false,
    controller_specs: true,
    request_specs: false
  g.fixture_replacement :factory_girl, dir: "spec/factories"
end
```

Now all generators should be using rspec instead of test unit.

Generate something simple such as:
```console
rails g controller hello index
```

Change default site root in `config/routes.rb` by adding:
```ruby
root to: 'hello#index'
```

## Bootstrap Theme
Install bootstrap 3.2 and rails_layout(for applying a default bootstrap theme to our site) installation:

Add these gems to gemfile:
```ruby
gem 'bootstrap-sass'
gem 'autoprefixer-rails'

group :development do
  gem 'rails_layout'
end
```

Install gems and generate bootstrap layouts:
```console
bundle install

rails generate layout:install bootstrap3 --force
```

## Authentication
Devise installation (simple - just to have a basic auth set up and styled)
Add to the gemfile:
```ruby
gem 'devise'
```
Install gem and run devise installation:
```console
bundle install

rails generate devise:install
```

Add to `config/environments/development.rb`:
```ruby
config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }
```

Create your user model:
```console
rails generate devise user

rake db:migrate
```

Add to navigation and stylize auth pages:
```console
rails generate layout:navigation --force
rails generate layout:devise bootstrap3
```

Ready to Go!
Play a little with auth by using the methods:
```ruby
before_action :authenticate_user!
user_signed_in?
current_user
user_session
```

You can delete the toy controller with:
```console
rails destroy controller hello
```

## Push to Github
*Github account is needed and git must be installed.*

Create a new repository on github website.

**Dont push files with sensitive date** like the `.env`. Write them in `.gitignore` file.

Make the root directory of the app a git repo:
```
git init
```

Add in all files & changes so far to a staging area.
```
git add .
```

Commit all files, adding the message "Initial commit"
```
git commit -m "Initial commit"
```

Create a connection, named "origin" pointing at the GitHub repository you just created.
```
git remote add origin https://github.com/your_username/your_newly_created_repo.git
```

Send your commits in your "master" branch to GitHub
```
git push -u origin master
```
