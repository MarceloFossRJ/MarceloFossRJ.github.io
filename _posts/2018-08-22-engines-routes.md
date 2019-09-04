---
layout: post
title:  "Using routes in Rails engines"
date:   2018-08-22 18:44:25 -0300
categories: [recipe]
tags: [rails, engines, routes, ror]
author: Marcelo Foss
intro: |
  No matter if you are writing a component based application in RoR, or if you are just using a gem in your project, if that gem do provide their own views, there is a big probability that you will want to override the engine routes.
---

No matter if you are writing a component based application in RoR, or if you are just using a gem in your project, if that gem do provide their own views, there is a big probability that you will want to override the engine routes.
That's what we want to handle here.

# First lets see how engines handle routes

Routes for the engine are defined just like a normal rails app, in the *routes.rb* file.
```ruby
AcaRails::Engine.routes.draw do
  resources :users
end
```

In order to activate those routings in base application, it’s routes.rb file also needs to be amended:
```ruby
Rails.application.routes.draw do
  ...
  mount AcaRails::Engine, at: "/aca_rails"
  ...
end
```

Check it in the rails application with
```
$ rake routes
```

```
Prefix Verb          URI Pattern                       Controller#Action
    categorias GET    /categorias(.:format)             categorias#index
               POST   /categorias(.:format)             categorias#create
 new_categoria GET    /categorias/new(.:format)         categorias#new
edit_categoria GET    /categorias/:id/edit(.:format)    categorias#edit
     categoria GET    /categorias/:id(.:format)         categorias#show
               PATCH  /categorias/:id(.:format)         categorias#update
               PUT    /categorias/:id(.:format)         categorias#update
               DELETE /categorias/:id(.:format)         categorias#destroy
          root GET    /                                 dashboards#index
     aca_rails        /aca                              AcaRails::Engine

Routes for AcaRails::Engine:
home_index GET    /home/index(.:format)               aca_rails/home#index
    locked GET    /locked(.:format)                   aca_rails/home#locked
    logout GET    /logout(.:format)                   aca_rails/sessions#destroy
     login GET    /login(.:format)                    aca_rails/sessions#new
           POST   /login(.:format)                    aca_rails/sessions#create
     users GET    /users(.:format)                    aca_rails/users#index
           POST   /users(.:format)                    aca_rails/users#create
  new_user GET    /users/new(.:format)                aca_rails/users#new
 edit_user GET    /users/:id/edit(.:format)           aca_rails/users#edit
      user GET    /users/:id(.:format)                aca_rails/users#show
           PATCH  /users/:id(.:format)                aca_rails/users#update
           PUT    /users/:id(.:format)                aca_rails/users#update
           DELETE /users/:id(.:format)                aca_rails/users#destroy
```

Notice how engine path is mounted under defined /aca_rails part. This allows us to have the same resources names in both main Rails application and mounted engine.   
The tricky part about routes is accessing base application routing helpers in engine and vice versa.

To access the current appliction routes all helpers must prefix helpers with *main_app*:

```ruby
<%= link_to "Home", main_app.root_path %>
```

To reference engine routes from main app, use the *aca_rails* prefix:

```ruby
<%= link_to "users", aca_rails.users_path %>
```

If engine is not namespaced, its routing proxy prefix would be *aca_rails_engine* instead of *aca_rails*.

Tip for curious ones: *main_app* and *aca_rails* are ActionDispatch::Routing::RoutesProxy instances.

# Overriding engine routes

My approach is to add the following code to the route I want to override in the engine initializer inside the main app.
If the engine/gem do not provide an initializer, you can create one.

```ruby
#/config/initializers/aca_rails.rb
...
AcaRails::Engine.routes.prepend do
  root to: '/dashboards#index'
end
```
