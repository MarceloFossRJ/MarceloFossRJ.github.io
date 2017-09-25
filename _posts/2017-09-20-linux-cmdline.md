---
layout: post
title:  "A quick Linux command line reference"
date:   2017-09-20 19:45:31 -0300
categories: [reference card]
tags: [linux]
author: Marcelo Foss
---
This post provides a shortcut for the most frequently used linux commands, for a wide variety of situations, from common file handling to checking system load.

The usual Linux command format is:
```
command -option(s) argument(s)
```
For example:
```
$rm -rf test_app
```

## Basic file and tree commands
* ls  
the list command - functions in the Linux terminal to show all of the major directories filed under a given file system  
-a : show hidden files
-lah : show file size in kb
-ltr : Order files based on last modified time

* cd [location]  
Changes the current working directory. Without an argument, it goes to the user’s home.

* mv [file] [location]  
Moves a file to another location.  
Also renames a file

* cp [file] [location]  
Copies a file to the specified location (overwriting the existing file silently).

* rm [file]  
Removes a file or directory
-rf : Removes a directory (recursively and forced, so it will not prompt)

* mkdir [directory]  
make directory - command allows the user to make a new directory.

* rmdir [directory]  
remove directory - allows the user to remove an empty directory

* pwd  
Display the pathname for the current directory

## File commands
* touch [file]  
The touch command - a.k.a. the make file command - allows users to make files using the Linux CLI.

* cat [file]  
Display file’s contents to terminal

* less [file]  
View the contents of a file one page at a time

* tail [file]  
Display the last n lines of a file (the default is 10)

* file [file]  
Determine what type of data is within a file.

* find  
Search for files matching a provided pattern.  
-name : find a file by the name  
-iname : find a file by the name ignoring the case  
```
$find -iname "users.rb"
```
Link with command detailed description: [https://www.computerhope.com/unix/ufind.htm](https://www.computerhope.com/unix/ufind.htm)
[http://www.thegeekstuff.com/2009/03/15-practical-linux-find-command-examples/](http://www.thegeekstuff.com/2009/03/15-practical-linux-find-command-examples/)

* grep  
Search files or output for a particular pattern  
Link with command detailed description: [https://www.computerhope.com/unix/ugrep.htm](https://www.computerhope.com/unix/ugrep.htm)  
[http://www.thegeekstuff.com/2009/03/15-practical-unix-grep-command-examples/](http://www.thegeekstuff.com/2009/03/15-practical-unix-grep-command-examples/)

* ln [file] [file]  
Create hard or symbolic link between files
```
$ln /path/to/file /path/to/link     #creates a hard link
$ln -s /path/to/file /path/to/link  #creates a symbolic link
```

Link with command detailed description:  
[https://www.lifewire.com/create-symbolic-links-ln-command-4059723](https://www.lifewire.com/create-symbolic-links-ln-command-4059723)  
[https://www.computerhope.com/unix/uln.htm](https://www.computerhope.com/unix/uln.htm)

* chmod [options] [permissions] [file]  
Makes a file executable and change files's permissions  
-x : make file executable  
The user can read, write, and execute it; members of your group can read and execute it; and
others may only read it:
```
$chmod u=rwx,g=rx,o=r myfile
$chmod 754 myfile
```

* chown [options] [file]  
Change who owns a file
-R : add recursive permissions  
```
$chown foss file.txt
$chown -R foss /files/work
```

* tar [options] [file/location]  
command used to work with tarballs ( files compressed in a tarball archive).
It can compress and extract files (preserving the file directory structure) from a tarfile (.tar) or tarball (.tar.gz or .tgz)  
To create a new tar file:  
```
$tar cvf archive_name.tar dirname/ # creates an archive
$tar xvf archive_name.tar  # unconpress a file
$tar tvf archive_name.tar  # view file contents
```
Link with command detailed description:  
[http://www.thegeekstuff.com/2010/04/unix-tar-command-examples/](http://www.thegeekstuff.com/2010/04/unix-tar-command-examples/)

* gzip [options] [file]  
command used to work with gz compressed files.  
-d : decompress a file  
```
$gzip test.txt  # compress a file
$gzip -d test.txt.gz # uncompress a file
```

* unzip [options] [file]  
command to uncompress a zip file:
```
$unzip test.zip
```

* nano [file]  
Used to edit configuration files.

* vi [file]  
Used to edit configuration files.

## User administration commands
* sudo [command]  
Executes a command as an administrative user.

* su  
Switch to another user account.

* adduser [username]  
Adds a user to the system.

* adduser [username] [groupname]  
Adds a user to a specific group.

* passwd  
Change the password or allow (for the system administrator) to
change any password.

* who  
Display who is logged on

## Basic admin commands
* uname  
used to show the information about the system your Linux distro is running
-a : show detailed information about the system
```
$uname -a
```

* apt-get dist-upgrade  
In addition to performing upgrades, this is used to intelligently handle dependencies.

* apt-get install [package]  
Installs (or upgrades) packages.

* apt-get update  
Resynchronize the package index files from their sources.

* apt-get upgrade  
Installs the newest versions of all packages currently installed.

* logout  
Logs out of a shell or SSH session.

* shutdown  
Shutdown the system  
-h : halt the system
-h now : shutdown now
-h +5 : shutdown the system after 5 minutes
-r now : shutdown and restart the system now
-Fr : checks the filesystem when restarting
```
$shutdown -h now
```

* reboot  
Reboots the system.

* source [script]  
Forces bash to read the specified script.

## Remote
* ssh [username@ipaddress]   
Logs into a secure shell session  
-l : login user name  
```
ssh -l foss remotehost.example.com
```

* wget [location]  
Obtains a file from the Internet  
-O : saves the file with a different name
```
$wget http://marcelofossrj.github.io/repos/file.zip
$wget -O file2.zip http://marcelofossrj.github.io/repos/file.zip
```

* curl  
a tool to transfer data from or to a server. It supports HTTP, HTTPS, FTP, FTPS, SCP, SFTP, TFTP, DICT, TELNET, LDAP or FILE  
Basic usage:  
```
$curl example.com  #shows the http content
$curl example.com > example.html #saves the the content
$curl -o example.html example.com #also saves the content
```
Link with command detailed description:  
[http://www.slashroot.in/curl-command-tutorial-linux-example-usage](http://www.slashroot.in/curl-command-tutorial-linux-example-usage)


## Services and processes commands
* df  
Display used and available disk space  
-m : show in megabytes  
-h : show output in human form  

* du  
Show how much space each file takes up  

* ps  
Display a snapshot of the currently running processes  
```
$ps -ef | more   #view running processes
$ps -efH | more  #view running processes in tree
```

* top  
Displays the resources being used on your system. Press q to exit  

* kill  
Stop a process. If the process refuses to stop, use kill -9 pid  

* Service  
Service command is used to run the system V init scripts. i.e Instead of calling the scripts located in the /etc/init.d/ directory with their full path, you can use the service command
```
$sudo service ssh restart
$sudo service nginx stop
$service --status-all
```

* free
Command used to display the free, used, and swap memory available in the system in bytes  
-g : shows in giga bytes  
-m : shows in mega bytes  
-k : shows in kilo bytes  


[comment]: <> (https://diyhacking.com/linux-commands-for-beginners/)
