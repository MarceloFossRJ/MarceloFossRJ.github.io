---
layout: post
title: Install Redis on MacOS
date:   2017-12-27 18:44:25 -0300
category: [recipe]
tags: [recipe, redis, cache, ]
author: Marcelo Foss
intro: |
---
Procedure to install Redis for MacOs High Sierra. Should work for previous versions running homebrew.
- Update 2018/05:
Works for MacOS Mojave

- Update 2019/09:
Works for MacOs Catalina

# Install using homebrew
```
$ brew update
$ brew install redis
```

# Starting Redis
To have launchd start redis now and restart at login:
```
brew services start redis
```

Or Start Redis server via “launchctl”.
```
$ launchctl load ~/Library/LaunchAgents/homebrew.mxcl.redis.plist
```

Or, if you don't want/need a background service you can just run:

```
redis-server /usr/local/etc/redis.conf
```

# Redis-Cli
Test if Redis server is running.

```
redis-cli ping
```
If it replies “PONG”, then it’s good to go!

# Configuration and management
Location of Redis configuration file.

```
/usr/local/etc/redis.conf
```

Stop Redis on autostart on computer start.
```
$ launchctl unload ~/Library/LaunchAgents/homebrew.mxcl.redis.plist
```

Uninstall Redis and its files.

```
brew uninstall redis
rm ~/Library/LaunchAgents/homebrew.mxcl.redis.plist
```
