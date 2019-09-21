---
layout: post
title:  "Running Rails app Locally in Production Environment"
date:   2017-08-23 09:45:11 -0300
categories: [recipe]
tags: [rails, production, ruby, ror, rails_env]
author: Marcelo Foss
---
1. Run `bundle install` to ensure the environment is updated
1. Run `RAILS_ENV=production rake db:create db:migrate db:seed`
1. Run `rake secret` and copy the output
1. Change `production.rb` file `config.assets.compile = false` to `config.assets.compile = true`  
1. From the command line: `export SECRET_KEY_BASE=output-of-rake-secret`
1. To precompile your assets, run `rake assets:precompile RAILS_ENV=production`. This will create a folder `public/assets` that contains all of your assets.
1. Run `RAILS_ENV=production rails s` and you should see your app.

When any change to source code is done, clobber your assets (`rake assets:clobber`) and re-precompile (`rake assets:precompile`).
