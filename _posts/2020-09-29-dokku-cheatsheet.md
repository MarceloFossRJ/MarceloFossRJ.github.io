---
layout: post
title: Dokku CheatSheet
date:   2020-09-29 18:44:25 -0300
category: [cheatsheet]
tags: [dokky, heroku, docker, devops, deploy, cheatsheet]
author: Marcelo Foss
intro: |
  The smallest PaaS implementation you’ve ever seen. Docker powered mini-Heroku in around 200 lines of Bash.
  Heroku has become the standard to host Ruby On Rails web applications. Deploying is a matter of typing “git push heroku master” and you’re pretty much done! The thing is, if you are part of a small development team or you are a freelancer, the cost of using Heroku for all your clients / projects might become a real issue for you. This is where Dokku comes in!
---

# Commands
## General app commands
action | command
------ | -------
create an app | `dokku apps:c­reate <na­me>`
clone one app | `dokku apps:clone <na­me>`
destroy app | `dokku apps:d­estroy <na­me>`
list existing apps | `dokku apps:list`
rename one app | `dokku apps:r­ename <na­me>`
get report for an app | `dokku apps:r­eport <na­me>`

## Process and container managment
action | command
------ | -------
List processes inside a container | `dokku ps:report <app>`
Rebuild an app from source | `dokku ps:rebuild <app>`
Restart process inside container | `dokku ps:restart <app>`
scale process inside an app | `ps:scale <app> <pr­oc>­=<c­oun­t> [<p­roc­>=<­cou­nt>]`
Start app container(s) | `ps:start <app>`
Stop app container(s) | `ps:stop <app>`
Sanitized version of docker inspect for an app | `ps:inspect <app>`

[Dokku docs link](https://github.com/dokku/dokku/blob/master/docs/deployment/process-management.md)

## Proxy NGINX and ports config­uration
action | command
------ | -------
enable proxy for app | `dokku proxy:­enable <ap­p>`
disable proxy | `dokku proxy:­disable <ap­p>`
show proxy status for app | `dokku proxy:­report <ap­p>`
list proxy ports | `dokku proxy:­ports <ap­p>`
add proxy port | `dokku proxy:­por­ts-add <ap­p> <sc­hem­e|h­ttp­>:<­hos­t-p­ort­|80­>:<­con­tai­ner­-po­rt|­500­0>`
remove proxy port | `dokku proxy:­por­ts-­remove <ap­p> <sc­hem­e|h­ttp­>:<­hos­t-p­ort­|80­>:<­con­tai­ner­-po­rt|­500­0>`
clear ports | `dokku proxy:­por­ts-­clear <ap­p>`

## Logs,info and ENV
action | command
------ | -------
get report for app | `dokku apps:r­eport [<a­pp>]`
get logs | `dokku logs <app>`
set env variable | `dokku config:set [--no-­res­tart] <app> <VA­RIA­BLE­>=<­VAL­UE>`
remove env variable | `dokku config­:unset [--no-­res­tart] <app> <VA­RIA­BLE­>=<­VAL­UE>`
get DB info | `dokku postgr­es:info <app>`
get proxy/­ports info `dokku proxy:­ports <app>`
know proxy is enable | `dokku proxy:­report <app>`
Check memory usage ! `docker stats`

## Postgres
action | command
------ | -------
create db | `dokku postgr­es:­create <na­me>`
link db to app | `dokku postgr­es:link <db­-na­me> <ap­p-n­ame>`
list databases | `dokku postgr­es:­list`
connect via psql | `dokku postgr­es:­connect <na­me>`
expose port for external connection | `dokku postgr­es:­expose <na­me>`
export dump | `dokku postgr­es:­export <na­me> > <fi­le>`
destroy db | `dokku postgr­es:­destroy <na­me>`
get info for db | `dokku postgr­es:info <na­me>`

[Dokku docs link](https://github.com/dokku/dokku-postgres)  

## Nginx
action | command
------ | -------
Show the nginx access logs for an app (-t follows) | `nginx:access-logs <app> [-t]`
Show the nginx error logs for an app (-t follows) | `nginx:error-logs <app> [-t]`
Displays a nginx report for one or more apps | `nginx:report [<app>] [<flag>]`
Display app nginx config | `nginx:show-config <app>`
Validates and optionally cleans up invalid nginx configurations | `nginx:validate-config [<app>] [--clean]`

[Dokku docs link](https://github.com/dokku/dokku/blob/master/docs/configuration/nginx.md)

## Domains
action | command
------ | -------
Add domains to app | `domains:add <app> <domain> [<domain> ...]`
Add global domain names | `domains:add-global <domain> [<domain> ...]`
Clear all domains for app | `domains:clear <app>`
Clear global domain names | domains:clear-global
Disable VHOST support | `domains:disable <app>`
Enable VHOST support | `domains:enable <app>`
Remove domains from app | `domains:remove <app> <domain> [<domain> ...]`
Remove global domain names | `domains:remove-global <domain> [<domain> ...]`
Displays a domains report for one or more apps | `domains:report [<app>|--global] [<flag>]`
Set domains for app | `domains:set <app> <domain> [<domain> ...]`
Set global domain names | `domains:set-global <domain> [<domain> ...]`

[Dokku docs link](https://github.com/dokku/dokku/blob/master/docs/configuration/domains.md)

## other commands

upload public key | `cat /home/­<us­er>­/.s­sh/­<pu­bli­c-k­ey>.pub | ssh <us­er>­@<i­p> "sudo sshcommand acl-add dokku <ke­y-n­ame­>"`
execute command inside docker container | `dokku --rm run <na­me> <co­mma­nd:­pyt­hon­|no­de|­npm­|> [args]`


# Dokku configiration & deploy
## Installing Dokku
- Download the install script from Dokku then run the script:
```
$ wget https://raw.githubusercontent.com/dokku/dokku/v0.12.5/bootstrap.sh
$ sudo DOKKU_TAG=v0.12.5 bash bootstrap.sh
```
- Navigate to the public IP address of your server in a browser and enter the public key

- Install Postgres database
```
$ dokku plugin:install https://github.com/dokku/dokku-postgres.git postgres
```

- Install Redis
```
sudo dokku plugin:install https://github.com/dokku/dokku-redis.git redis
```

- Install plugin for SSH with LetsEncrypt
```
$ sudo dokku plugin:install https://github.com/dokku/dokku-letsencrypt.git
```


## Deploy a static application to dokku

### @ Dokku server
- Create dokku app
```
$ dokku apps:create mydokkustaticapp
```
- Make sure that VHOST is enabled:
```
$ dokku domains:enable mydokkustaticapp
```
Create environment variable for NGINX container with the path where the static site is. If it is a Jekyll site would be:
```
$ dokku config:set mydokkustaticapp NGINX_ROOT=_site
```

### @ Development server
- Create a blank file in the project root directory named `.static`
```
$ touch .static
```
- Create a file named `.buildpacks` in the root directory to load the Dokku pre-build NGINX container
```
$ touch .buildpacks
$ vi buildpacks
https://github.com/dokku/buildpack-nginx
```
- Create dokku remote
```
$ git remote add dokku dokku@mydomain.com:mydokkustaticapp
```
- Push files to Dokku
```
$ git push dokku master
```

### @ Dokku server again
- Set up persistent storage so NGINX can serve media files (jpg, png, pdf, mov, etc)
```
$ mkdir -p  /var/lib/dokku/data/storage/mydokkustaticapp
$ chown -R dokku:dokku /var/lib/dokku/data/storage/mydokkustaticapp
$ chown -R 32767:32767 /var/lib/dokku/data/storage/mydokkustaticapp
$ sudo dokku storage:mount mydokkustaticapp /var/lib/dokku/data/storage/mydokkustaticapp:/assets/images
$ mkdir -p /home/dokku/mydokkustaticapp/nginx.conf.d
$ nano /home/dokku/mydokkustaticapp/nginx.conf.d/static.conf
$ dokku ps:restart mydokkustaticapp
```

- The application is available as a subdomain, at mydokkustaticapp.mydomain.com. If you want to have the application available at a custom domain (like www.mydokkustaticapp.com), add the domain using the domain plug in (installed by default):
```
$ dokku domains:add mydokkustaticapp mydokkustaticapp.com *.mydokkustaticapp.com
```
- SSL Certificate with Dokku and Let’s EncryptPermalink
Install the Let’s Encrypt plugin for Dokku:
```
$ sudo dokku plugin:install https://github.com/dokku/dokku-letsencrypt.git
```
Set the DOKKU_LETSENCRYPT_EMAIL environment variable to the email for Let’s Encrypt:
```
$ dokku config:set mydokkustaticapp DOKKU_LETSENCRYPT_EMAIL=myemail@mydomain.com
```
Add the application and domain:
```
$ dokku domains:add mydokkustaticapp mydomain.com
```
Create the SSL certificate. NGINX will automatically start serving the application over HTTPS on port 443:
```
$ dokku letsencrypt mydokkustaticapp
```
Run this as a cron job so the certificate will renew automatically:.
```
$ dokku letsencrypt:cron-job --add
```

## Deploy a Rails app to dokku
### @ Dokku server (or using dokku-cli gem in your project root)
- Create dokku app
```
$ dokku apps:create myrailsapp
```
- Make sure that VHOST is enabled:
```
$ dokku domains:enable myrailsapp
```
- Link dokku postgres to your app
```
$ dokku postgres:create db-myrailsapp
$ dokku postgr­es:link db-myrailsapp myrailsapp
```
- link dokku redis to app
```
dokku redis:create redis-myrailsapp
$ dokku redis:link redis-myrailsapp myrailsapp
```
- Set ENV variables
You can set environment variables with the following commands:
```
$ dokku config:set APPLICATION_NAME=myrailsapp
```

### @ Development server
- Create app.json

- Create procfile to initialize sidekiq and puma

- create dokku remote
- sync environment variables to dokku: sudo bash script.sh
create .buildpacks
push files to dokku
dokku ps:scale appname web=2 worker=3


# Set up monitoration tools
TODO

# References and resources

[https://medium.com/@dpaluy/the-ultimate-guide-to-dokku-and-ruby-on-rails-5-9ecad2dba4a3](https://medium.com/@dpaluy/the-ultimate-guide-to-dokku-and-ruby-on-rails-5-9ecad2dba4a3)

[https://cheat.readthedocs.io/en/latest/django/dokku_admin.html](https://cheat.readthedocs.io/en/latest/django/dokku_admin.html)

[http://www.rubyfleebie.com/2018/12/14/how-to-use-dokku-on-digitalocean-and-deploy-rails-applications-like-a-pro/](http://www.rubyfleebie.com/2018/12/14/how-to-use-dokku-on-digitalocean-and-deploy-rails-applications-like-a-pro/)

[https://pawelurbanek.com/rails-heroku-dokku-migration](https://pawelurbanek.com/rails-heroku-dokku-migration)

[https://pawelurbanek.com/optimize-dokku-deployment-speed](https://pawelurbanek.com/optimize-dokku-deployment-speed)

[https://www.aloucaslabs.com/miniposts/how-to-install-sidekiq-to-a-ruby-on-rails-project-deployed-on-dokku-server](https://www.aloucaslabs.com/miniposts/how-to-install-sidekiq-to-a-ruby-on-rails-project-deployed-on-dokku-server)

[https://medium.com/@email_99995/minha-experiência-utilizando-dokku-com-sistemas-ruby-on-rails-fb971732986b](https://medium.com/@email_99995/minha-experiência-utilizando-dokku-com-sistemas-ruby-on-rails-fb971732986b)

[https://genox.ch/en/blog/deploying-docker-images-to-dokku-in-a-gitlab-pipeline](https://genox.ch/en/blog/deploying-docker-images-to-dokku-in-a-gitlab-pipeline)

[https://github.com/dokku/dokku/tree/master/docs](https://github.com/dokku/dokku/tree/master/docs)

[https://www.papertrail.com/blog/monitoring-dokku-container-logs-with-solarwinds-papertrail/](https://www.papertrail.com/blog/monitoring-dokku-container-logs-with-solarwinds-papertrail/)

[https://www.milanvit.net/post/getting-started-with-server-monitoring-and-alerting/](https://www.milanvit.net/post/getting-started-with-server-monitoring-and-alerting/)

[https://letsencrypt.org/getting-started/](https://letsencrypt.org/getting-started/)
