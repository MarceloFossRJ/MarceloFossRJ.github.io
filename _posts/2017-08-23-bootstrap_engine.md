---
layout: post
title:  "Recipe to Use Bootstrap with Rails Engines"
date:   2017-08-23 09:45:11 -0300
categories: [recipe]
tags: [rails, engines, gem, components, ror, bootstrap]
author: Marcelo Foss
---
There are several gems that setup bootstrap for Rails apps, but they are created targeting plain Rails applications, and they don't work properly on Rails engines.  
If you are going to use bootstrap in a plain Rails app the gem twitter-bootstrap-rails, is good one to use.  
I prefer to manually install it, it is easy, and you have total control what is happening.  

### First step: Download bootstrap
For the article, I am going to use the brand new bootstrap 4, that now is in the alpha 6 version, so not formally available in a stable release.  
**IMPORTANT:**  
If you are going to apply it for a production environment I do recommend to use bootstrap 3 (currently version 3.3.7).  
The configuration procedure is the same.  
Depending when you read that article the version can be different, beta version, or maybe version 4 is already in a stable realease, so please check if the latest official release.  

Go to [https://v4-alpha.getbootstrap.com/](https://v4-alpha.getbootstrap.com/) and download the latest version (for version 3, go to [http://getbootstrap.com/](http://getbootstrap.com/))

Uncompress the the downloaded file anywhere in your system. You are going to see the follwing forder structure:
```
/bootstrap-4.0.0-alpha.6-dist
	/css
		bootstrap.css
		bootstrap.css.map
		bootstrap.min.css
		bootstrap.min.css.map
		bootstrap-grid.css
		bootstrap-grid.css.map
		bootstrap-grid.min.css
		bootstrap-grid.min.css.map
		Bootstrap-reboot.css
		bootstrap-reboot.css.map
		bootstrap-reboot.min.css
		bootstrap-reboot.min.css.map
	/js
		bootstrap.js
		bootstrap-min.js
```

### Bootstrap configuration
On the engine root folder, create a vendor folder, and replicate the following folder structure:
```
<<EngineName>>/
	/vendor
		/assets
			/javascripts
				/<<EngineName>>
			/stylesheets
				/<<EngineName>>
```
```			
$mkdir vendor
$mkdir vendor/assets
$mkdir vendor/assets/javascripts
$mkdir vendor/assets/stylesheets
$mkdir vendor/assets/javascripts/<<EngineName>>
$mkdir vendor/assets/stylesheets/<<EngineName>>
```

Copy all the /bootstrap-4.0.0-alpha.6-dist/css files into the <<EngineName>>/vendor/assets/stylesheets/<<EngineName>>

Copy all the /bootstrap-4.0.0-alpha.6-dist/js files into the <<EngineName>>/vendor/assets/javascripts/<<EngineName>>

### Jquery.js and jquery_ujs
Bootstrap needs Jquery installed.  
A normal Rails application have it installed by default.  
A Rails Engine do not, as well as the dummy app.  
Go to [https://jquery.com/download/](https://jquery.com/download/) and download the latest production version. At time of writing the article we have the 3.2.1 version.  
After downloading it, copy it to the recently created ```<<EngineName>>/vendor/assets/javascripts/<<EngineName>>``` folder.  

To install the jquery_ujs (which is needed for example, for the delete links), you have to go to the github repository at ```https://github.com/rails/jquery-ujs``` and download the hole zip file for the project.  
You are going to actually use the rails.js file in the src folder.  
After downloading it, copy it to the recently created ```<<EngineName>>/vendor/assets/javascripts/<<EngineName>>``` folder

Tooltips rely on the 3rd party library Tether for positioning.  
Download it at [https://github.com/HubSpot/tether/blob/master/dist/js/tether.min.js](https://github.com/HubSpot/tether/blob/master/dist/js/tether.min.js)
copy to the ```<<EngineName>>/vendor/assets/javascripts/<<EngineName>>``` folder, and reference it on the application.js
You must include ```tether.min.js``` before ```bootstrap.js``` in order for tooltips to work!  

### Application.css
Edit the application.css file and leave the lines below:

```javascript
/*
 *= require <<EngineName>>/bootstrap
 *= require_self
*/
```

### Application.js
Edit the application.js file and leave the lines below:

```javascript
//= require <<EngineName>>/jquery-3.2.1.min
//= require <<EngineName>>/rails
//= require_tree .
//= require <<EngineName>>/tether.min
//= require <<EngineName>>/bootstrap.min

$(function () {
    $('[data-toggle="tooltip"]').tooltip()
})
```

### Pagination with Kaminari
Add the following line to engine .gemspec
``` ruby
#GemName.gemspec
s.add_dependency "kaminari", "~>1.0.1"
```

Add the following line to the Gemfile
```ruby
#Gemfile
gem "kaminari", "~>1.0.1"
```
run the bundler to install the gems
```
$bundle install
```
Execute the following command to generate the kaminari initializer in the ```config/initializers/``` folder

```
$rails g kaminari:config
```
To have the paginator working with bootstrap, run the following command
```
$rails g kaminari:views bootstrap4
```
It will create the folder ```app/views/kaminari``` with the files that overhide the default paginator.  
You can change or customize as you want this files.
If you find any error running the generator, all it does is copying the files of the theme into app/views/kaminari/.  
As a workaround you can just copy them from the master branch yourself from [https://github.com/amatsuda/kaminari_themes/](https://github.com/amatsuda/kaminari_themes/){:target="\_blank"}

To have it properly working with engines, you have to move the newly created folder inside the engine namespace: ```app/views/<<EngineName>>/```
