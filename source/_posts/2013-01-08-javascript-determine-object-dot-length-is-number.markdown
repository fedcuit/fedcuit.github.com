---
layout: post
title: obj.length === +obj.length in javascript 
date: 2013-01-08 13:55
comments: true
categories: javascript underscore
---
晚上看Underscore.js源码的时候，看到这样的一种写法:
``` javascript obj.lenght === +obj.length in underscore.js https://github.com/documentcloud/underscore/blob/master/underscore.js#L79 Source
else if (obj.length === +obj.length)
```
在SO上搜索后得知原来这种写法原来跟`if (typeof obj.length == 'number')`是等价的，之所以要写成上面的那种形式的原因是前者比后者要节省5个字节的空间。。。
