---
layout: post
title: Ubuntu Configuration
date: 2013-05-27 21:04
comments: true
categories: linux ubuntu
---

**apt-get**

* setup proxy for apt-get

```bash
sudo gedit /etc/apt/apt.conf #This is a new file
```
add `Acquire::http::Proxy "http://username:password@proxyhost:port/"` to this file

**Gnome desktop**

* create application shortcut

```bash
  sudo apt-get install --no-install-recommends gnome-panel
  gnome-desktop-item-edit ~/Desktop/ --create-new
```

