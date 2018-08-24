---
layout: post
title:  "Caching Strategies and Rails apps"
date:   2017-11-07 19:45:31 -0300
categories: [article]
tags: [ruby, ror, architecture, caching]
author: Marcelo Foss
---
Caching is the procedure to store frequently accessed data in order to have it available so subsequent requests can be load faster without the need to produce the data again.

 From rails guides:
 *"Caching means to store content generated during the request-response cycle and to reuse it when responding to similar requests.  
Caching is often the most effective way to boost an application's performance. Through caching, web sites running on a single server with a single database can sustain a load of thousands of concurrent users.  
Rails provides a set of caching features out of the box. This guide will teach you the scope and purpose of each one of them. Master these techniques and your Rails applications can serve millions of views without exorbitant response times or server bills."*  
[http://guides.rubyonrails.org/caching_with_rails.html](http://guides.rubyonrails.org/caching_with_rails.html)

Requests in web applications are expensive, one single page can make several requests to load completely, connections to databases and querying databases is also expensive.   
API calls are also expensive. How many images and what is the sizes of each one? What about the search? So as you can see there are many aspects to take in consideration before applying any caching strategy.
The first step is to have a target or a benchmark that you need to achieve, then you should analyze how you can speed up page loading in your application, to finally decide what is the best approach to each situation.

# Setting up a target

# Profiling
## Client-side
http://railscasts.com/episodes/369-client-side-performance?view=asciicast
### Use chrome extension
### Use webpagetest.org
[http://www.webpagetest.org/](http://www.webpagetest.org/)
### Google chrome audits
### Google PgeSpeed insigths
[https://developers.google.com/speed/pagespeed/insights/?hl=pt-br](https://developers.google.com/speed/pagespeed/insights/?hl=pt-br)
### Apache bench

```
$ ab -t 10 -c 10 http://localhost:3000/
```

The -t option controls how long we’re going to benchmark for (in seconds), and -c controls the number of requests that we’ll try at the same time. Set the -c option based on your production load - if you have more than an average of 1 request per second (per server), it would be good to increase the -c option approximately according to the formula of (Production requests per minute / production servers or dynes) * 2. I usually test with at least -c 2 to flush out any weird threading/concurrency errors I might have accidentally committed.

### rack-mini-profiler

# Key-based cache expiration
In the old days, Rails developers used to do a lot of manual cache expiration, with Observers and Sweepers. Nowadays, we try to avoid these entirely, and instead use something called key-based expiration.

# HTTP Caching
[https://www.mnot.net/cache_docs/](https://www.mnot.net/cache_docs/)
HTTP caching is the cache that is managed via HTTP headers. As it is part of the HTTP specification i is often stored in the user’s web browser.
Check the excellent tutorial by [Mark Nottingham](https://www.mnot.net/cache_docs/).   
There is also a very good tutorial from [Ryan Bates](http://railscasts.com/episodes/321-http-caching?view=asciicast).  

## ETags
ETags allow browsers to make conditional GET requests when accessing a document more than once from the same host, and only pull down the full document body if the document has actually changed. If both host and client are configured to support conditional GET, the host will send an ETag header down with the document, which is a short identifying string for the version of the document that it is sending. On subsequent requests to the same URI, the client will send a If-None-Match request header set to the value of the ETag and if the document hasn’t changed on the server, the server will send a 304 Not Modified response down with an empty body and the client will pull the content it was previously sent out of it’s own cache to display to the user.

Rails supports ETags and conditional GET via the stale? and fresh_when controller methods. For example, to render a users#show action with support for conditional GET, we might write:

```ruby
#app/controllers/users_controller.rbRuby
class UsersController < ApplicationController
  before_action :load_user

  def show
    fresh_when etag: @user
  end

  private

  def load_user
    @user = User.find(params[:id])
  end
end
```

## Last-modified

An important consideration when implementing ETags and conditional GET in Rails is that by default ETag caches will not be busted when you deploy template changes (unless you update the stock RAILS_CACHE_ID on deploy as well, but this also often means unnecessarily clearing out your fragment caches). You should look to a library like Nathan Kontny’s bust_rails_etags, which overrides the default Rails ETag methods to also take into account an ETAG_VERSION_ID environment variable, which you can set in a way that suits your deployment scheme.


# Varnish
https://varnish-cache.org/
http://blog.hostdime.com.br/materias/tecnologia/varnish-cache-o-que-e-e-como-implementa-lo/

# Memcached vs Redis
When deciding between Memcached and Redis, here are a few questions to consider:

* Is object caching your primary goal, for example to offload your database? If so, use Memcached.
* Are you interested in as simple a caching model as possible? If so, use Memcached.
* Are you planning on running large cache nodes, and require multithreaded performance with utilization of multiple cores? If so, use Memcached.
* Do you want the ability to scale your cache horizontally as you grow? If so, use Memcached.
* Does your app need to atomically increment or decrement counters? If so, use either Redis or Memcached.
* Are you looking for more advanced data types, such as lists, hashes, and sets? If so, use Redis.
* Does sorting and ranking datasets in memory help you, such as with leaderboards? If so, use Redis.
* Are publish and subscribe (pub/sub) capabilities of use to your application? If so, use Redis.
* Is persistence of your key store important? If so, use Redis.

https://www.quora.com/What-are-the-differences-between-Memcached-and-Redis-in-features-and-performance
https://stackoverflow.com/questions/10558465/memcached-vs-redis



Following are various strategies used for managing data updates in cache:

* Configuration data is cached only during application startup. Any subsequent updates to the configuration while the application is running are not applicable unless the cache is refreshed or in other words application server is restarted.  
This strategy is often used in applications where transactions should be executed within few seconds. The application server is restarted as a planned maintenance activity. The drawback of this strategy is that the configuration updates are applicable only after application startup. Any change in the configuration data would require restarting application server which could increase the frequency of restarting application server during maintenance as the number of configuration data updates increase. Planned downtime of application increases as the number of application server restarts increase. Hence, this strategy works best for applications which are deployed on Intranet or applications which have less frequent configuration updates.

* Database tables having configuration data are periodically accessed and the cache is updated if any data change is found. EJB timers or cron jobs are used to perform this activity. The application doesn’t need to be restarted. Applications which are up 24 X 7, for e.g. shopping sites, maintain data such as product data in the cache. Periodic updates to the data such as change in product price or exchange rates are made using this strategy.

* Event-based publish-subscribe design pattern is used to apply changes in cache. Any database update done for the data which is also maintained in the cache, triggers an event which updates the cached data. Observer design pattern can be used to trigger an event for database update.

* Updates to the database are followed by cache updates. In this strategy both cache as well are database are updated simultaneously.

The caching strategy is decided based on the scope and impact of data updates. Use of 2nd or 3rd strategy or their combination is more common in applications for minor data updates. Change in the application workflow, for example credit scoring rules, is usually done using the 1st or 2nd strategy. Locking mechanisms such as obtaining table level or row level lock in the database or optimistic or pessimistic locking or thread-level locking is used to ensure that cache is not accessed by the application in runtime mode while the cached data is being modified in configuration mode. Use of optimistic locking is more common.

Data in cache is usually maintained as in-memory static objects in the application server JVM. The use of caching technologies such as EhCache or static blocks within POJO classes, static Java objects, singleton EJBs is very common while implementing caching in code.


!!!! https://www.speedshop.co/2015/07/15/the-complete-guide-to-rails-caching.html

http://hackernotes.io/2017/04/09/caching-strategies-rails-5-applications/

http://hawkins.io/2012/07/advanced_caching_revised/
http://hawkins.io/2011/05/advanced_caching_in_rails/

https://devcenter.heroku.com/articles/caching-strategies

http://guides.rubyonrails.org/caching_with_rails.html

https://www.sitepoint.com/speed-things-up-by-learning-about-caching-in-rails/
