---
layout: post
title:  "MongoDB + PostGres + RoR"
date:   2019-04-02 19:45:31 -0300
categories: [reference card]
tags: [database, document, nosql, mongodb, rails, ruby, RoR, db]
author: Marcelo Foss
---

## First steps
* Create the rails application:
```
$ rails new mongo-psql --database=postgresql
```

## Setup rails config.yml for Postgres connection
* The `config/database.yml` will look like this:

```yaml
default: &default
  adapter: postgresql
  encoding: unicode
  timeout: 5000
  # For details on connection pooling, see Rails configuration guide
  # http://guides.rubyonrails.org/configuring.html#database-pooling
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>

development:
  <<: *default
  database: mongo-psql_development
```

## Create the model mapping for ActiveRecord
* Generate resourcers for a User model, using ActiveRecord model:
```
$ rails g scaffold User name:string email:string
```
* The genereated migration file `db/migrate/*_create_users.rb` will look like:

```ruby
class CreateUsers < ActiveRecord::Migration[5.2]
  def change
    create_table :users do |t|
      t.string :name
      t.string :email
      t.timestamps
    end
  end
end

```

## Test Postgres connection
* Run
```
$ rails console
> User.create!(name: “Root”, email: “root@test.com”)
```

## Create Mongo connection for Rails application
There are several GEMs to interact with MongoDB. I used the MongoID GEM.
Mongoid is a ODM (object document mapper) framework for Mongo in Ruby.
You can find more information [HERE](https://docs.mongodb.com/mongoid/current/).

* Append to MongoID to your gemfile
``` ruby
gem "mongoid", "~> 7.0.2"
```
* Run the Mongoid configuration installation script:
```
$ rails g mongoid:config
```
Which creates the config/mongoid.yml

* Configure Rails model generator
when you install the mongoid gem and run its configuration script, mongoid becomes the default database for all generators. If you wish to keep the generetor to the SQL database (in this case PostgreSql), inside the Application class (config/application.rb)  add the following:
```ruby
config.generators do |g|
  g.orm :active_record
end
```

Now if you want to generate the model with MOngoDb then use following command.
```
$ rails g model Test --orm=Mongoid
OR
$ rails g mongoid:model Phone
```


## Create Mongoid model files
* Run `rails g model Phone`
* Change `app/model/phone.rb` to:

```ruby
class Phone
  include Mongoid::Document
  include Mongoid::Timestamps

  field :user_id, type: Integer
  field :number, type: String
end
```

## MongoDb and PostGreSql relationships

* Define how `user.rb` will use phone:
```ruby
class User < ActiveRecord::Base

  def phone
    Phone.where(user_id: self.id).order_by({created_at: -1}).one
  end

  def phone=(phone)
    Phone.create!(user_id: self.id, status: status)
  end

  def phones
    Phone.where(user_id: self.id).order_by({created_at: -1})
  end

end
```

* Define how `phone.rb` will use user:
```ruby
class Phone
  include Mongoid::Document
  include Mongoid::Timestamps

  field :user_id, type: Integer
  field :number, type: String

  def user
    User.find(self.user_id)
  end
end
```
