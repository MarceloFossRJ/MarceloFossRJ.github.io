---
layout: post
title:  "Recipe to Use paper_trail with Rails Engines"
date:   2017-08-22 15:22:20 -0300
categories: [recipe]
tags: [rails, engines, gem, components, ror, paper_trail]
author: Marcelo Foss
---
How to set up paper_trail to use in an rails engine.

Add the line to engine .gemspec
``` ruby
#GemName.gemspec
s.add_dependency "paper_trail", "~> 7.0.3"
```

Add the following line to the Gemfile
```ruby
#Gemfile
gem "paper_trail", "~> 7.0.3"
```
run the bundler to install the gems
```
$bundle install
```

The paper_trail installer helper will not work in the engine, due to the missing rails structure.  
Change to the dummy application folder, ```spec/dummy/``` and execute the installer command:
```
$rails g paper_trail:install
```
The installer copies two files to the rails structure:  
the migration for the versions table, and the paper trail initializer.  
Move those files to the engine structure.  

By default the engine does not have a ```config/initializers``` folder.  
It could have been already created by another gem (like simple_form).  
If it's not the case, create it and copy the file.  

That is the same for ```db/migrations```, if you have not yet created a model or so, the folder is not there.  
If that is the case, create it and copy the file.

### Using custom version classes

If a single class (table) is used to store the versions, it can become quite big...  
I prefer the approach of using a version class to each model in the application.  
To configure paper_trail you need to:

1. create a new model, and its associated migration
```ruby
#app/models/gem_name/tenant_versions.rb
module GemName
  class TenantVersion < PaperTrail::Version
        self.table_name = :gem_name_tenant_versions
  end
end
```
```ruby
# db/migrate/20170707020124_create_gem_name_tenant_versions.rb
class CreateGemNameTenantVersions < ActiveRecord::Migration[5.0]
  TEXT_BYTES = 1_073_741_823
  def change
    create_table :gem_name_tenant_versions do |t|
      t.string   :item_type, {:null=>false}
      t.integer  :item_id,   null: false
      t.string   :event,     null: false
      t.string   :whodunnit
      t.text     :object, limit: TEXT_BYTES
	  t.text     :object_changes

      t.datetime :created_at
    end
    add_index :gem_name_tenant_versions, %i(item_type item_id)
  end
end
```
Note in the migration the line with the field *object_changes*, which is not added by default by paper_trail.  
According to Paper_trail documentation:  
*"The best way to diff adjacent versions is to get PaperTrail to do it for you. If you add an **object_changes** text column to your versions table, either at installation time with the ```rails generate paper_trail:install --with-changes ```  option or manually, PaperTrail will store the changes diff (excluding any attributes PaperTrail is ignoring) in each update version. You can use the version.changeset method to retrieve it."*

2. If you only use custom version classes and don't have a versions table, you must let ActiveRecord know that the *PaperTrail::Version* class is an abstract_class.
```ruby
# app/models/paper_trail/version.rb
module PaperTrail
  class Version < ActiveRecord::Base
       include PaperTrail::VersionConcern
       self.abstract_class = true
  end
end
```
3. Apply to the model file
```ruby
# app/models/gem_name/tenant.rb
module GemName
  class Tenant < ApplicationRecord
        has_paper_trail class_name: 'GemName::TenantVersion'
  end
end
```
