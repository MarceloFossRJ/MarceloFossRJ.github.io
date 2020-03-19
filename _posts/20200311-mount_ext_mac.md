---
layout: post
title:  "H to mount an EXT partition on a Mac"
date:   2020-03-11 18:44:25 -0300
categories: [recipe]
tags: [MacOS, macos-x, Linux ]
author: [Marcelo Foss, Folusho Oladipo]
---
This is an useful article originally published by Folusho Oladipo at [Hack my Linux](https://hackmylinux.com/2018/02/18/how-to-mount-and-read-a-linux-partition-on-a-mac-ext2-ext3-ext4/)

# HOW TO MOUNT AN EXT PARTITION ON A MAC

I have been using a MacBook for a while now but was shocked to learn that the Mac OS can’t mount ext2/ext3/ext4 file systems by default. Unbelievable. I mean, at its core, Mac OS and Linux both belong to the Unix family of operating systems (proof) and ext is the default filesystem of Linux. So if the Mac OS can mount the filesystems of Windows (NTFS and FAT32) by default, it should be able to mount the ext filesystem too, right? Wrong. So I consulted Google for a solution but found that there was no quick, pretty and free software for this. Even the paid solutions I read about were not totally reliable and were known to corrupt people’s storage devices. Nevertheless, here is the best info I found about how you can mount an ext2/ext3/ext4 partition on a Mac.

## Mounting an ext2/ext3/ext4 partition on Mac OS

When trying to mount an ext2/3/4 partition on your Mac, you have at least two options:

### Option 1: Mount the partition as read-only (RO).

With this option, you can only access the files on your partition, you cannot modify them. You can copy your movies, photos etc out of the partition but you cannot delete, rename, cut or add any files/directories. I recommend this option because it poses an almost zero risk of corrupting your files or the partitions they are stored in. If this suits you, here are the steps you need to take to achieve it:
- Make sure you have a good internet connection.
- Make sure you have Homebrew installed. If you don’t, visit this page to set it up.
- Make sure you have administrator rights for the machine you want to work on. If you don’t, get someone who has those rights to do the rest of these steps for you.
- Open a terminal (or command prompt) on your machine.
- Install the package ext4fuse with the following commands:
```
$ brew cask install osxfuse
$ brew install ext4fuse
```
Plug in the hard disk drive or storage device whose partition you want to mount.
In the terminal, enter the command below to see a list of all the storage devices attached to your computer:
```
$ diskutil list
```
Take note of the id of the partition you wish to mount. You’ll have to identify that partition by yourself based on its size, name etc as listed by diskutil.  
Let’s say the desired partition has an id of disk3s1, and it is mounted on /dev. Use the command below to mount it as a drive named MY_EXT_PARTITION in the tmp/ directory of your home directory:
```
$ sudo ext4fuse /dev/disk3s1 ~/tmp/MY_EXT_PARTITION -o allow_other
```
Note that the command above uses sudo, so it requires administrator privileges to work. After entering it, you might be asked to enter an admin account’s password.  
It is also possible to mount more than one ext partition from your hard disk drive. If, for example, the id of the second partition you want to mount is disk3s2, then you can mount it as a drive named MY_SECOND_EXT_PARTITION in the tmp/ directory with this command:
```
$ sudo ext4fuse /dev/disk3s2 ~/tmp/MY_SECOND_EXT_PARTITION -o allow_other
```
Now use Finder to navigate to your home directory and then your tmp/ directory. You should see a device named MY_EXT_PARTITION in that location. PRO TIP: You can also achieve do this step by entering the following command in the terminal:
```
$ open ~/tmp/
```
Note that you can mount the partition anywhere you like and use any name you wish for the mounted drive. Using the ```tmp/``` directory is only for the sake of convention, as that directory is meant for temporary data. But if, for example, you wish to mount the partition in your ```Documents/``` directory and want to call it ```foo_bar```, you’ll use the following command:
```
$ sudo ext4fuse /dev/disk3s1 ~/Documents/foo_bar -o allow_other
```

### Option 2: Mount the partition as read-write (RW).
You will be able to both access your files/directories and modify them with this option. So, you will be able to add, rename, delete or move the ```files/directories``` stored on your partition along with being able to access them. Unfortunately I have not found a stable, reliable and free way to do this. Even the paid software I read about that claim to be able to do this are buggy and have been reported to cause damage to people’s files/partitions. So, I do not recommend this option. You may use Google to find out how people have done it but please proceed with caution.
