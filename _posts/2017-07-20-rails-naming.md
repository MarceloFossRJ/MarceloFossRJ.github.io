---
layout: post
title:  "Rails naming convention"
date:   2017-07-20 20:32:34 -0300
categories: [guide]
tags: [ruby, ror, rails, standard]
author: Marcelo Foss
---

Rails use the same naming convention as Ruby with some additions:  
[Link the Ruby Naming Convention post]({% post_url 2017-07-15-ruby-naming %})

**Files, Directories and other pluralization**    
Files are named using lowercase and underscores. Assuming we have an Users controller then the following other conventions will apply:

* That there is a helper module named **UsersHelper** in the **users_helper.rb** found in the app/helpers directory.
* Rails will look for view template files for the controller in the **app/views/users** directory.
* Output from this view will then be used in the layout defined in the **users.html.erb** in the **app/views/layouts** directory.
* Test files including **user_test.rb** will be created in the **/test/unit** directory, a file will be created in the **/test/fixtures** directory called **users.yml** and finally a file called **users_controller_test.rb** will be created in the **/test/functional directory**

**Variables** - *i.e. user_status, total*  
Variables are named where all letters are lowercase and words are separated by underscores.

**Class and Module** - *i.e. UserPermissions*  
Classes and modules use MixedCase and have no underscores, each word starts with a uppercase letter.

**Controller** - *i.e. UsersController*  
Controller class names are pluralized, such that **UsersController would be the controller class for the users table**.  Rails will then look for the class definition in a file called users_controller.rb in the /app/controllers directory.

**Model** - *i.e. User*  
The model is named using the class naming convention of unbroken MixedCase and is always the singular of the table name.  
For example:  
* Table name might be users (plural)
* The model name would be User (singular)
* Rails will then look for the class definition in a file called user.rb in the /app/models directory.
* If the model class name has multiple capitalised words, the table name is assumed to have underscores between these words.

**Database Table** - *e.g. user_permissions, users*  
Table names have all lowercase letters and underscores between words, also all table names need to be plural.

**Primary Key**  
The primary key of a table is assumed to be named id.

**Foreign Key**  
The foreign key is named with the singular version of the target table name with _id appended to it, e.g. order_id in the items table where we have items linked to the orders table.

**Many to Many Link Tables**  
Tables used to join two tables in a many to many relationship is named using the table names they link, with the table names in alphabetical order, for example categories_users.

**Automated Record Timestamps**  
You can get ActiveRecord to automatically update the create and update times of records in a database table. To do this create two specially named columns created_at and updated_at to your table, i.e. t.datetime :created_at and t.datetime :updated_at. If you only want to store the date rather than a date and time, use :created_on and :updated_on.

### Naming Convention Summary

#### Model Naming Convention

	Table: 		users
	Class: 		User
	File: 		/app/models/user.rb
	Primary Key: 	id
	Foreign Key: 	client_id
	Link Tables: 	products_users

#### Controller Naming Convention

	Class: 		UsersController
	File: 		/app/controllers/users_controller.rb
	Layout: 	/app/layouts/users.html.erb

#### View Naming Convention

	Helper: 	/app/helpers/users_helper.rb
	Helper Module:  UsersHelper
	Views: 		/app/views/users/… (index.html.erb for example)

#### Tests Naming Convention

	Unit: 		/test/unit/user_test.rb
	Functional: 	/test/functional/users_controller_test.rb
	Fixtures: 	/test/fixtures/users.yml
