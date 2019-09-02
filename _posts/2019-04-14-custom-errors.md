---
layout: post
title: Custom rails error pages
date:   2018-04-14 18:44:25 -0300
category: [recipe]
tags: [Rails, Ruby, Error, Exception]
author: Marcelo Foss
intro: |
  How to handle nice error pages in an Rails application.
---
# Custom error pages for Rails apps
Normally, 404 and 500 error pages are static HTML files that live in the public directory of a Rails application. These are boring, minimally-styled pages that don’t get the same treatment as the rest of the app. This tutorial shows you how to move error pages into your Rails app as dynamic views that benefit from application styles, layouts, and view helpers.

There are different options to render a custom merror page in your Rails app. My option is for dynamic error pages.


## Dynamic error pages

First Remove the static HTML error pages
```
 $ rm public/{404,422,500}.html
```
Set your own application routes as Rails’ exception app
```
 # config/environments/production.rb
 config.exceptions_app = self.routes
```
Add some routes for each error type
```
 # config/routes.rb

 # Dynamic error pages
 get "/404", to: "errors#not_found", :via => :all
 get "/422", to: "errors#unacceptable", :via => :all
 get "/500", to: "errors#internal_error", :via => :all
```
Create an ErrorsController. Because Void is API based I also take care to render JSON error responses
```
 class ErrorsController < ApplicationController
   before_action :set_status

   def not_found
     respond_to do |format|
       format.html { render status: 404 }
       format.json { render json: { error: "Resource not found" }, status: 404 }
     end
   end

   def unacceptable
     respond_to do |format|
       format.html { render status: 422 }
       format.json { render json: { error: "Params unacceptable" }, status: 422 }
     end
   end

   def internal_error
     respond_to do |format|
       format.html { render status: 500 }
       format.json { render json: { error: "Internal server error" }, status: 500 }
     end
   end
   def set_status
      @exception = env['action_dispatch.exception']
      @status    = ActionDispatch::ExceptionWrapper.new(env, @exception).status_code
      @response  = ActionDispatch::ExceptionWrapper.rescue_responses[@exception.class.name]
   end
 end
 ```
Create views for each error action app/views/errors/{internal_error,not_found,unacceptable}.html.erb
```
 <!-- example app/views/errors/not_found.html.erb -->
 <h1>Page not found</h1>
 <p>Whoops we couldn’t find the page you were looking for!</p>
```

Dynamic error pages are not without fault however, you still need static pages as a fallback for when your application errors due to your hosting provider/PaaS having unexpected downtime or incase you bump into a long running database migration when deploying.

If you use heroku you can configure your maintenance and error pages by running the following command:
```
$ heroku config:set \
  ERROR_PAGE_URL=//s3.amazonaws.com/<your_bucket>/your_error_page.html \
  MAINTENANCE_PAGE_URL=//s3.amazonaws.com/<your_bucket>/your_maintenance_page.html
```

## DRAWBACKS
Dynamic error pages let us use the power of the Rails view layer, but this has its own drawbacks.

If the error page has errors. Syntax errors, database outages, or other catastrophes can lead to dynamic error pages that themselves fail to render. If this happens, not only can’t users interact with your app, they won’t be able to see your fancy error page!

Luckily Rails is smart enough to recognize this situation an avoid an infinite loop. As a last resort, Rails will display a simple plaintext error message:

500 Internal Server Error

If you are the administrator of this website, then please read this web application’s log file and/or the web server’s log file to find out what went wrong.

If Rails has completely crashed. When a Rails application is proxied by a web server like Nginx, the web server can be configured to serve static files from public/. Theoretically, if your Rails application completely crashed, Nginx could still serve a static error page, like public/500.html.

But with dynamic error pages this is not possible. By definition, Rails has to be up and running in order for those error pages to be displayed. You’ll need a static error page for this scenario.

So let’s generate one!

## BONUS: AUTO-GENERATING A STATIC ERROR PAGE WITH CAPISTRANO
Assuming you deploy using Capistrano 3, you can use Capistrano to also generate a static public/500.html page whenever your application is deployed. With proper Nginix configuration, this error page can be served even in the unfortunate scenario when your Rails app is completely offline.

1 DEFINE A CAPISTRANO TASK
```
task :generate_500_html do
  on roles(:web) do |host|
    public_500_html = File.join(release_path, "public/500.html")
    execute :curl, "-k", "https://#{host.hostname}/500", "> #{public_500_html}"
  end
end
after "deploy:published", :generate_500_html
```
This instructs Capistrano to request the /500 route of your application and save the resulting HTML to public/500.html. This happens on every successful deploy. Now your app has a static 500 error page that looks just like your dymamic one, automatically!

[https://mattbrictson.com/dynamic-rails-error-pages](https://mattbrictson.com/dynamic-rails-error-pages)
[https://pooreffort.com/blog/custom-rails-error-pages/](https://pooreffort.com/blog/custom-rails-error-pages/)
[https://coderwall.com/p/whjmra/handling-exceptions-in-your-rails-application](https://coderwall.com/p/whjmra/handling-exceptions-in-your-rails-application)
