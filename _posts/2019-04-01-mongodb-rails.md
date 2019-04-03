layout: post
title:  "MongoDB + RoR Quick Start"
date:   2019-04-01 19:45:31 -0300
categories: [reference card]
tags: [database, document, nosql, mongodb, rails, ruby, RoR, db]
author: Marcelo Foss
---

## First steps

### Application generation  
When you set up your initial Rails project make sure to skip active record.
```
rails new my_app --skip-active-record
```
Active record uses migrations to control your database, which won’t be needed with MongoDB. Use this option if you are going to use MongoDb only. If you're going to use together a SQL database, keep ActiveRecord.

### Mongoid driver configuration
There are several GEMs to interact with MongoDB. I used the MongoID GEM.
Mongoid is a ODM (object document mapper) framework for Mongo in Ruby.
You can find more information [HERE](https://docs.mongodb.com/mongoid/current/).
``` ruby
# gemfile
...
gem ‘mongoid’, '>= 7.0.2'
...
```
You will also need to make sure that you run the config
```
$ rails generate mongoid:config
```
Here is an example of what to expect the config file to look like (minus a big chuck of the comments):

``` ruby
development:
  # Configure available database clients. (required)
  clients:
    # Defines the default client. (required)
    default:
      # Defines the name of the default database that Mongoid can connect to.
      # (required).
      database: my_app_development
      # Provides the hosts the default client can connect to. Must be an array
      # of host:port pairs. (required)
      hosts:
        - localhost:27017
      options:
        # Change the default write concern. (default = { w: 1 })
        # write:
        #   w: 1

        # Change the default read preference. Valid options for mode are: :secondary,
        # :secondary_preferred, :primary, :primary_preferred, :nearest
        # (default: primary)
        # read:
        #   mode: :secondary_preferred
        #   tag_sets:
        #     - use: web
 seconds.
        # (default: 5)
        # connect_timeout: 5

        # The timeout to wait to execute operations on a socket before raising an error.
        # (default: 5)
        # socket_timeout: 5

        # The name of the replica set to connect to. Servers provided as seeds that do
      certifications
  options:
test:
  clients:
    default:
      database: my_app_test
      hosts:
        - localhost:27017
      options:
        read:
          mode: :primary
        max_pool_size: 1
```

## MongoDB Data Modelling
The key point to note here is it not inheriting from ActiveRecord. Documents are the core objects in Mongoid and any object that is to be persisted to the database must include Mongoid::Document which can be seen below:

### MongoDB based model
```ruby
class Person
include Mongoid::Document

field :first_name, type: String
field :last_name, type: String
end
```
Mongoid by default stores documents in a collection in a pluralized form of the class name. For the above Person class, the collection the document would get stored in would be named people.

### Some quick model configurations

* Defining a new field :
```ruby
field :name, type: String
field :_id, type: String, default: ->{ name }
```
* Localized fields
```ruby
field :description, localize: true
```
* Indexing
```ruby
index “description.de” => 1
index “description.en” => 1
```
* Relations
```ruby
class User
include Mongoid::Document
field :name, type: String
embeds_many :posts
end

class Post
include Mongoid::Document
field :x, type: Integer
field :y, type: Integer
embedded_in :user
end
```
