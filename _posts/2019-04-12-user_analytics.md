---
layout: post
title:  "Getting Visitor Analytics for Rails apps"
date:   2019-04-12 19:45:31 -0300
categories: [article]
tags: [web analytics, web, rails, ruby]
author: Marcelo Foss
---
Very interesting article *'User Analytics for Rails Apps'* written by **Bala Paranj** at *Rubyplus.com*, here is the link of the original post: [https://rubyplus.com/articles/4051-User-Analytics-for-Rails-Apps](https://rubyplus.com/articles/4051-User-Analytics-for-Rails-Apps){:target="\_blank"}

## Getting IP Address
* Extract the IP from the request in the controller:
```ruby
request.remote_ip
```

* Getting Location details from IP
Use `Geocoder` gem.
``` ruby
> r = Geocoder.search('209.249.19.172').first
 => #<Geocoder::Result::Freegeoip:0x007fc02ae9ad80 @data={"ip"=>"209.249.19.172", "country_code"=>"US", "country_name"=>"United States", "region_code"=>"CA", "region_name"=>"California", "city"=>"Moraga", "zip_code"=>"94556", "time_zone"=>"America/Los_Angeles", "latitude"=>37.8381, "longitude"=>-122.1026, "metro_code"=>807}, @cache_hit=nil>
> r.class
 => Geocoder::Result::Freegeoip
> r.data
 => {"ip"=>"209.249.19.172", "country_code"=>"US", "country_name"=>"United States", "region_code"=>"CA", "region_name"=>"California", "city"=>"Moraga", "zip_code"=>"94556", "time_zone"=>"America/Los_Angeles", "latitude"=>37.8381, "longitude"=>-122.1026, "metro_code"=>807}
> r.data['ip']
 => "209.249.19.172"
> r.data['country_name']
 => "United States"
> r.data['region_name']
 => "California"
> r.data['city']
 => "Moraga"
> r.ip
 => "209.249.19.172"
> r.country
 => "United States"
> r.state
 => "California"
> r.postal_code
 => "94556"
> r.city
 => "Moraga"
 ```
Save the IP in the database and geocode in the background using background jobs.

## Getting the Search Keyword
Use `referer-parser` gem.
```ruby
require 'referer-parser'
rf = RefererParser::Parser.new.parse('http://www.google.com/search?q=gateway+oracle+cards+denise+linn&hl=en&client=safari')
 => {:known=>true, :uri=>"http://www.google.com/search?q=gateway+oracle+cards+denise+linn&hl=en&client=safari", :source=>"Google", :medium=>"search", :domain=>"google.com", :term=>"gateway oracle cards denise linn"}
> rf.class
 => Hash
> rf
 => {:known=>true, :uri=>"http://www.google.com/search?q=gateway+oracle+cards+denise+linn&hl=en&client=safari", :source=>"Google", :medium=>"search", :domain=>"google.com", :term=>"gateway oracle cards denise linn"}
> rf[:term]
 => "gateway oracle cards denise linn"
> rf[:domain]
 => "google.com"
> rf[:uri]
 => "http://www.google.com/search?q=gateway+oracle+cards+denise+linn&hl=en&client=safari"
> rf[:source]
 => "Google"
 ```
In the controller pass `request.referer` to the parse method. Save the `request.referer` in the database and use referer-parser gem to extract details in a background job. This will not work if the user is signed-in to Google.

You will get the error:
```
Exception occurred : Only HTTP and HTTPS schemes are supported --
```
for direct traffic source. The reason is referer value will be nil and the library will throw this meaningless exception. You can identify direct traffic source:
```ruby
if request.referer.nil?
  logger.info 'Traffice source : Direct'
end
```
The precondition is not satisfied. So, ideally the library should throw an exception saying that it cannot parse nil to find the referer.

## Find the Referring Domain
Use `addressable` gem. Add it to Gemfile.
```ruby
gem 'addressable'
```
The value of `request.referer` provides the referring domain. The standard URI library in ruby sometimes drops parts of the URL. It also has problems handling special characters in URLs like these: ™ ‘ ’ ° ®. Addressable ruby gem from sporkmonger on git hub is a perfect solution. So use this gem to find the referring domain.
```ruby
referring_domain = Addressable::URI.parse('http://www.google.com/search?q=gateway+oracle+cards+denise+linn&hl=en&client=safari').host
 => "www.google.com"
 ```
Save the request.referer in the database. Do this processing in a background job.

## Browser Details
User `browser` gem to extract browser related information.
```ruby
require "browser"
> user_agent = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.112 Safari/537.36'
 => "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.112 Safari/537.36"
    browser = Browser.new(user_agent, accept_language: "en-us")
 => #<Browser::Chrome:0x007f9772913488 @ua="Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.112 Safari/537.36", @accept_language=[#<Browser::AcceptLanguage:0x007f9772913348 @part="en-us", @quality=1.0>]>
> browser.name
 => "Chrome"
> browser.bot?
 => false
> browser.version
 => "49"
> browser.device
 => #<Browser::Device:0x007f97729621c8 @ua="Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.112 Safari/537.36">
> browser.device.name
 => "Unknown"
> browser.device.id
 => :unknown
> browser.platform
 => #<Browser::Platform:0x007f97730b8e68 @ua="Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.112 Safari/537.36">
> browser.platform.name
 => "Macintosh"
```
You can pass the user agent to the browser constructor by calling:
```ruby
request.user_agent
```
in the controller.

## Tracking New Visitors
In the controller, set a cookie when a visitor comes to your site if they don't already have value for ma_visit cookie.
```ruby
unless browser.bot?
  unless cookies[:ma_visit].present?
    # Sets a "permanent" cookie (which expires in 20 years from now).
    cookies.permanent[:ma_visit] = "some-unique-string-for-every-browser"
  end
end
```
A visit is new if the ma_visit cookie does not exist.
```ruby
def new_visit?
  !existing_visit_id
end

def existing_visit_id
  request.cookies["ma_visit"])
end
```
To generate the unique string for the cookie use `uuidtools` gem. Add it to Gemfile.
```ruby
gem 'uuidtools'
```
You can create an unique string like this:
```ruby
> UUIDTools::UUID.random_create.to_s
 => "a5cd193b-b23f-4541-9238-8522b809e9e1"
```

## User Agent Details
You can use `user_agent_parser` gem.
```ruby
> require 'user_agent_parser'
 => false
> user_agent = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.112 Safari/537.36'
 => "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.112 Safari/537.36"
> ua = UserAgentParser.parse user_agent
 => #<UserAgentParser::UserAgent Chrome 49.0.2623 (Mac OS X 10.10.5) (Other)>
> ua.to_s
 => "Chrome 49.0.2623"
> ua.family
 => "Chrome"
> ua.version
 => #<UserAgentParser::Version 49.0.2623>
> ua.version.to_s
 => "49.0.2623"
> ua.version.major
 => "49"
> ua.version.minor
 => "0"
> ua.os.to_s
 => "Mac OS X 10.10.5"
 ```
These tips are based on the source code of ahoy_matey gem. It uses user_agent_parser gem to find the browser name and the OS. We can use the browser gem to give us that information.

## References
[referer-parser gem](https://github.com/snowplow/referer-parser){:target="\_blank"}  
[Geocoder Gem](https://github.com/alexreisner/geocoder){:target="\_blank"}  
[Browser Gem](https://github.com/fnando/browser){:target="\_blank"}  
[Replacing Ruby's URI with Addressable](http://cloudspace.com/blog/2009/05/26/replacing-rubys-uri-with-addressable#.XK5eGS-ZPOQ){:target="\_blank"}  
