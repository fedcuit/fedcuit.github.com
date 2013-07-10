---
layout: post
title: Run headless selenium in Jenkins
date: 2013-07-10 23:28
comments: true
categories: jenkins ubuntu selenium headless
---

* Install vnc4server
```bash
sudo apt-get install vnc4server
```
* Install Xvnc plugin for Jenkins
* Switch to jenkins user and set password for vnc4server
```bash
$ su - jenkins
$ vncserver
> Enter a password, and verify it
$ vncserver -kill :1 # or whichever display the vncserver output mentioned
```
* Config Jenkins job to run in headless mode by check "Run Xvnc during build"

* Watch the running job
  * open any VNC client
  * connect to your.server:59xx (xx is the display number output on the Jenkins console for the running job)
  * enter the password you set for vnc4server before

This article is based on [From Zero to Headless Browser Tests in Jenkins](http://rapaul.com/2011/06/05/zero-to-headless-browser-tests-jenkins/)
