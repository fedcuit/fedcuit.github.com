---
layout: post
title: "常见的 python http library使用"
date: 2012-08-04 13:49
comments: true
categories: python http library
---

* urllib2
``` python
import urllib2

response = urllib2.urlopen('http://www.google.com')
response.read() #return content of requsted url in string format
```

