---
layout: post
title: Ubuntu Configuration
date: 2013-05-27 21:04
comments: true
categories: linux ubuntu
---

**apt-get**

* Setup proxy for apt-get

```bash
sudo gedit /etc/apt/apt.conf #This is a new file
```
add `Acquire::http::Proxy "http://username:password@proxyhost:port/"` to this file

**Gnome desktop**

* Create application shortcut

```bash
  sudo apt-get install --no-install-recommends gnome-panel
  gnome-desktop-item-edit ~/Desktop/ --create-new
```

**Setup ftp server**

* Install vsftpd `sudo apt-get install vsftpd`

* Edit `/etc/vsftpd.conf`
  * `anonymous_enable`
  * `write_enable`
  * `ftpd_banner`

* Save the file and restart ftp service by `sudo /etc/init.d/vsftpd restart`

* Copy files you want to shared to folder `/srv/ftp`
