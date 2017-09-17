---
layout: post
title:  "Linux commmand line reference"
date:   2017-07-10 19:45:31 -0300
categories: [reference card]
tags: [linux]
author: Marcelo Foss
---

A Linux command usually takes the form of
```
command -option(s) argument(s)
```
For example, rm -r test_app. For a more detailed description, use the manual, which can be accessed using man [command].

* sudo [command]  
Executes a command as an administrative user.
* adduser [username]  
Adds a user to the system.
* adduser [username] [groupname]  
Adds a user to a specific group.
* apt-get dist-upgrade  
In addition to performing upgrades, this is used to intelligently handle dependencies.
* apt-get install [package]  
Installs (or upgrades) packages.
* apt-get update  
Resynchronize the package index files from their sources.
* apt-get upgrade  
Installs the newest versions of all packages currently installed.
* bash < <( curl [location] )  
A combination of commands used to both obtain and execute a script (from the Internet).
* cd [location]  
Changes the current working directory. Without an argument, it goes to the user’s home.
* cp [file] [location]  
Copies a file to the specified location (overwriting the existing file silently).
* chmod +x [file]  
Makes a file exexutable.
* logout  
Logs out of a shell or SSH session.
* mv [file] [location]  
Moves a file to another location.
* nano [file]  
Used to edit configuration files.
* reboot  
Reboots the system.
* rm [file]  
Removes a file.
* rm -rf [directory]  
Removes a directory (recursively and forced, so it will not prompt).
* source [script]  
Forces bash to read the specified script.
* ssh [username@ipaddress]  
Logs into a secure shell session.
* type [command]  
Displays the kind of command that the shell will execute.
* wget [location]  
Obtains a file from the Internet.
