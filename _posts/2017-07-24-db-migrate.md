---
layout: post
title:  "Rails db:migrate reference card"
date:   2017-07-24 17:12:01 -0300
categories: [reference card]
tags: [ror, rails, ruby, migration, database]
author: Marcelo Foss
---
### Creating migrations
#### Rails generator
```
$bin/rails generate migration AddPartNumberToProducts
```
If the migration name is of the form "AddXXXToYYY" or "RemoveXXXFromYYY" and is followed by a list of column names and types then a migration containing the appropriate add_column and remove_column statements will be created.

Commands to create migrations  
```
$rails generate model Product name:string description:text
$rails generate migration AddPartNumberToProducts part_number:string
$rails generate migration RemovePartNumberFromProducts part_number:string
$rails generate migration AddDetailsToProducts part_number:string price:decimal
```

#### Migrations methods:
* add_column
* add_index
* change_column
* change_table
* create_table
* drop_table
* remove_column
* remove_index
* rename_column

#### Supported types

* :binary
* :boolean
* :date
* :datetime
* :decimal
* :float
* :integer
* :primary_key
* :string
* :text
* :time
* :timestamp
especial type:
* :references

To Add a column to a table
```
class AddPartNumberToProducts < ActiveRecord::Migration[5.0]
  def change
    add_column :products, :part_number, :string
  end
end
```
To remove a column from a table
```

class RemovePartNumberFromProducts < ActiveRecord::Migration[5.0]
  def change
    remove_column :products, :part_number, :string
  end
end
```

### DB Rake tasks :
Execute:  
```
rake -T db
```
* **db:migrate** - runs migrations for the current env that have not run yet (options: VERSION=x, VERBOSE=false)
* **db:migrate:up** - runs one specific migration
* **db:migrate:down** - rolls back one specific migration
* **db:migrate:status** - shows current migration status

* **db:create** - creates the database for the current env
* **db:create:all** - creates the databases for all envs

* **db:drop** - drops the database for the current env
* **db:drop:all** - drops the databases for all envs

* **db:rollback** - rolls back the last migration (specify steps w/ STEP=n)
* **db:forward** - advances the current schema version to the next one
* **db:seed** - (only) runs the db/seed.rb file
* **db:schema:load** - loads the schema into the current env's database
* **db:schema:dump** - dumps the current env's schema (and seems to create the db as well)
* **db:setup** - runs db:schema:load, db:seed
* **db:reset** - runs db:drop db:setup, create the database, load the schema, and initialize with the seed data (use db:reset to also drop the db first)
* **db:migrate:redo** - runs (db:migrate:down db:migrate:up) or (db:rollback db:migrate) depending on the specified migration
* **db:migrate:reset** - runs db:drop db:create db:migrate  
* **db:test:prepare** - Rebuild it from scratch according to the specs defined in the development database  
* **rake db:fixtures:load** - Load fixtures into the current environment's database.  
* **rake db:version** - Retrieves the current schema version number  

```
$rake db:migrate VERSION=20080906120000
$rake db:rollback
$rake db:rollback STEP=3
$rake db:migrate:redo STEP=3
$rake db:reset  #drop database and recreate it
$rake db:migrate:up VERSION=20080906120000
```

Further information at:  
[http://guides.rubyonrails.org/migrations.html](http://guides.rubyonrails.org/migrations.html)
