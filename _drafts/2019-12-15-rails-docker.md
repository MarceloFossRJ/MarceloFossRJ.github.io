---
layout: post
title: Setup Rails app with Docker-Compose
date:   2019-12-15 18:44:25 -0300
category: [recipe]
tags: [docker, kubernetes, devops, deploy, recipe, rails, ruby, docker-compose]
author: Marcelo Foss
intro: |
---

In this article, the goal is to dockerize a Ruby on Rails application. The application we’re going to build will make use of Nginx, Puma, PostgreSQL, Redis, and Sidekiq.

It is not the goal here to tell the reasoning or the benefits of using Docker, or how to install it. I leave it for other nice articles around this topic.

- Postgres is used as database
- Redis is used as cache and websocket
- Nginx is the HTTP server
- Postgres and Redis use Docker volumes to manage persistence
- Postgres, Redis, Nginx and the Rails app all expose a port
- Rails app connects to Postgres and Redis.
- Nginx connects to the the Rails app
- Nginx and the Rails app read environment variables from .env

## Prerequisites
The only needed prerequisite is to have Docker installed in the target hardware.
Check if docker and docker-compose are installed properly:
```
$ docker --version
Docker version 17.03.1-ce, build c6d412e

$ docker-compose --version
docker-compose version 1.12.0, build b31ff33
```

### .dockerignore file
Create a ```.dokerignore``` file in he root of your project:
```
.git
.gitignore
/doc
.yardoc
coverage
jsdoc
/tmp
/log
Dockerfile
Dockerfile.nginx
docker-compose.yml
README.md
/public/uploads
/test/reports
.env
.envrc
.rvmrc
.byebug_history
.rake_tasks
```

## Rails configuration
Firt lets build the rails container by defining its Dockerfile, which will create container with ruby, nodejs, imagemagick and yarn, application will be copied to container /app/rails-chat directory.

### Rails Dockerfile
```
FROM ruby:2.6.5-buster
MAINTAINER marcelo.foss.rj@gmail.com

# To prevent rake version uncompatibility
RUN gem update rake

# Package update, install nodejs, yarn, imagemagick
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg -o /root/yarn-pubkey.gpg \
    && apt-key add /root/yarn-pubkey.gpg \
    && echo "deb https://dl.yarnpkg.com/debian/ stable main" > /etc/apt/sources.list.d/yarn.list \
    && apt-get update \
    && apt-get install -y --no-install-recommends nodejs yarn \
    && apt-get install -y build-essential libpq-dev imagemagick

# Copy application code
ADD rails-chat/Gemfile /app/rails-chat/
ADD rails-chat/Gemfile.lock /app/rails-chat/

# Change to the application's directory
ENV RAILS_ROOT /app/rails-chat
WORKDIR $RAILS_ROOT

# Set Rails environment to production
ENV RAILS_ENV production

# Install gems, nodejs and precompile the assets
RUN bundle install

# Copy application code
COPY rails-chat/ .

ENTRYPOINT ./entrypoint.sh
```

A important step is the creation of the shell script ```entrypoint.sh```, which is executed right after the machine is booted.

```
# kill anything running at :3000
kill -9 $(lsof -i tcp:3000 -t)

# Compile the assets
rails assets:precompile

# Run missing migrations
rake db:migrate

# Start the server
bundle exec rails server
```

Don't forget to make it executable:
```
$ chmod +x ./entrypoint.sh
```

### Puma configuration

config/puma.rb
```ruby
threads_count = ENV.fetch("RAILS_MAX_THREADS") { 5 }
threads threads_count, threads_count

# Specifies the `port` that Puma will listen on to receive requests; default is 3000.
port        ENV.fetch("PORT") { 3000 }

# Specifies the `environment` that Puma will run in.
environment ENV.fetch("RAILS_ENV") { "development" }
workers ENV.fetch("WEB_CONCURRENCY") { 2 }
preload_app!
plugin :tmp_restart
```

### Database configuration

config/database.yml
```YAML
development:
  url: <%= ENV['DATABASE_URL'].gsub('?', '_development?') %>

test:
  url: <%= ENV['DATABASE_URL'].gsub('?', '_test?') %>

staging:
  url: <%= ENV['DATABASE_URL'].gsub('?', '_staging?') %>

production:
  url: <%= ENV['DATABASE_URL'].gsub('?', '_production?') %>
```

### Action cable configuration

config/cable.yml
```YAML
development:
  adapter: redis
  url: <%= ENV.fetch("REDIS_URL") { "redis://localhost:6379/1" } %>
  channel_prefix: rails-chat_development

test:
  adapter: async

production:
  adapter: redis
  url: <%= ENV.fetch("REDIS_URL") %>
  channel_prefix: rails-chat_production
```

### Production environment configuration
config/environments/production.rb
```ruby
  config.cache_classes = true

  config.eager_load = true

  config.consider_all_requests_local       = false
  config.action_controller.perform_caching = true

  config.public_file_server.enabled = ENV['RAILS_SERVE_STATIC_FILES'].present?

  # Compress JavaScripts and CSS.
  config.assets.js_compressor = :uglifier

  config.assets.compile = true

  config.active_storage.service = :local

  config.log_level = :debug

  config.log_tags = [ :request_id ]

  config.action_mailer.perform_caching = false

  config.i18n.fallbacks = true

  config.active_support.deprecation = :notify

  config.log_formatter = ::Logger::Formatter.new

  if ENV["RAILS_LOG_TO_STDOUT"].present?
    logger           = ActiveSupport::Logger.new(STDOUT)
    logger.formatter = config.log_formatter
    config.logger    = ActiveSupport::TaggedLogging.new(logger)
  end

  # Do not dump schema after migrations.
  config.active_record.dump_schema_after_migration = false
end

```

## Nginx configuration
Let's create nginx upstream to listen application port 3000.

### Nginx Dockerfile
Creat a Dockerfile.nginx file:
```
FROM nginx:latest
MAINTAINER marcelo.foss.rj@gmail.com

# Install dependencies
RUN apt-get update -qq \
    && apt-get -y install apache2-utils

# establish where Nginx should look for files
ENV RAILS_ROOT /app/rails-chat
RUN mkdir -p $RAILS_ROOT

# Set our working directory inside the image
WORKDIR $RAILS_ROOT

# create log directory
RUN mkdir log/

# copy over static assets
#COPY public public/

# Copy Nginx config
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
EXPOSE 4000

# Use the "exec" form of CMD so Nginx shuts down gracefully on SIGTERM (i.e. `docker stop`)
CMD [ "nginx", "-g", "daemon off;" ]
```

### Nginx configuration
nginx.conf
```
upstream rails-chat {
  server rails:3000;
}
server {
  listen 4000;
  client_max_body_size 4G;
  keepalive_timeout 10;
  error_page 500 502 504 /500.html;
  error_page 503 @503;
  server_name localhost rails-chat;
  root /app/rails-chat/public;

  # send non-static file requests to the app server
  location / {
    try_files $uri @rails-chat;
  }

  location @rails-chat {
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_redirect off;
    proxy_pass http://rails-chat;
    access_log /app/rails-chat/log/nginx.access.log;
    error_log /app/rails-chat/log/nginx.error.log;
  }

  # deny requests for files that should never be accessed
  location ~ /\. {
    deny all;
  }

  location ~* ^.+\.(rb|log)$ {
    deny all;
  }

  # serve static (compiled) assets directly if they exist (for rails production)
   location ~ ^/(assets|images|javascripts|stylesheets|swfs|system)/ {
     try_files $uri @rails-chat;

     access_log off;
     gzip_static on; # to serve pre-gzipped version

     expires max;
     add_header Cache-Control public;

     # Some browsers still send conditional-GET requests if there's a
     # Last-Modified header or an ETag header even if they haven't
     # reached the expiry date sent in the Expires header.
     add_header Last-Modified "";
     add_header ETag "";
     break;
   }
}
```
Note the upstream, its listening web container port 3000.

## Define Docker Compose file
Lets define web, db and app containers and link them to make it work together.
docker-compose.yml
```
version: '3'
services:
   redis:
      image: 'redis:latest'
      ports:
         - "6379:6379"
      volumes:
         - rails-chat-redis:/var/lib/redis/data
   db:
      image: 'postgres:9.4.1'
      environment:
         - POSTGRES_USER=${POSTGRES_USER}
         - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
      ports:
         - '5432:5432'
      volumes:
         - rails-chat-db:/var/lib/postgresql/data
   rails:
      build: .
      ports:
         - "3000:3000"
      depends_on:
         - db
         - redis
      environment:
         - DATABASE_URL=${DATABASE_URL}
         - REDIS_URL=${REDIS_URL}
   web:
      build:
         context: .
         dockerfile: ./Dockerfile.nginx
      depends_on:
        - rails
      ports:
        - '4000:4000'
volumes:
  rails-chat-db:
  rails-chat-redis:
```

### Create Volumes

```
$ docker volume create --name rails-chat-db
$ docker volume create --name rails-chat-redis
```

### .env file
```
POSTGRES_PASSWORD=rails_chat_pwd
POSTGRES_USER=rails_chat
DATABASE_URL=postgresql://rails_chat:rails_chat_pwd@db:5432/rails_chat?encoding=utf8&pool=5&timeout=5000

RAILS_MAX_THREADS=5

REDIS_URL=redis://redis:6379/1
```


webserver- Nginx Container configuration. Its going to expose port 80

db - Database Postgresql Container configuration.
Its going to expose port 5432

app - Rails application configuration.
Its going to start webrick server on port 3000.
Note the db volumes, it will share the postgres data with host machine, so that data will be persist even after re-create the container.

Find the container environment variables.
```
docker-compose build
```
```
docker-compose up
```
