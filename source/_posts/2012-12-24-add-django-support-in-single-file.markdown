---
layout: post
title: 为单个文件添加Django支持 
date: 2012-12-24 22:30
comments: true
categories: python django settings path 
---
``` python
import os
#using your own django project settings module name(not file name)
os.environ['DJANGO_SETTINGS_MODULE'] = "datawinners.settings" 
```
    必须先完成`DJANGO_SETTINGS_MODULE`的配置，然后再使用Django提供的功能。

在配置`DJANGO_SETTINGS_MODULE`之前，需要先配置`PYTHONPATH`, Django在执行的时候会从`PYTHONPATH`中去查找`DJANGO_SETTINGS_MODULE`指定的模块。`PYTHONPATH`跟Java的`CLASSPATH`作用上是一样的.

``` bash
export PYTHONPATH="/home/twer/workspace/datawinners/datawinners"
```
