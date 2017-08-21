---
layout: post
title:  "Facetime camera fix"
date:   2017-08-09 14:22:20 -0300
categories: [recipe]
tags: [macosx, el capitan, facetime]
author: Marcelo Foss
---
The problem: Facetime Camera Not Working.  
I have OSX El Capitain running in my Macbook Pro, and from time to time, the facetime camera stops working. 

To fix this problem, do the following:

1. Close all the applications that are using the camera (such as FaceTime and Skype).  
2. Open a terminal (Launchpad -> Terminal).  
3. Type the following command:  
``` 
$sudo killall VDCAssistant
```
4. Re-open your application. You should see that the camera is working now.  

When an application requires the camera, Mac OS X launches a background process called “VDCAssistant”. If application has any issue to properly close this process, it will block the resources and they will not be available for other applications, hence preventing other apps to access the camera.  
Forcing this process to be close, releases the resources that will then be available to other applications.

This approach should fix this issue 99% of the time. For the remaining 1%, you might just need to restart your Mac.
The following applies also to external cameras connected to your Mac (i.e. a USB camera).

