---
layout: post
title: 查看Jar包内容
date: 2013-01-31 22:28
comments: true
categories: jar java 
---

已经有快一年没有写过Java代码了，这段时间看Gradle, 发现Java社区还是发展挺快的( 亦或是我已经out很久了 ^_^ ), 以前的打包工作都是Ant或是Maven来完成的，如果接触过Django或是Rails的人看到Ant跟Maven里的那一大堆配置，一定都快吐了，现在好了，基于Groovy的Gradle的代码让这一切变得不再那么麻烦，只需使用`gradle build`就可以生成jar包了.

不知道什么原因，我在使用生成的jar文件时总是提示类找不到，感觉应该是生成的jar文件有问题，Google了查看jar文件内容的命令, 在这里分享一下:
``` bash 
jar tvf filename.jar 
```

 Option    |  Description
 ----      | -------------------------------------------------
 t         | Lists the table of contents from jarfile 
 v         | Generates verbose output to standard output 
 f         | Specifies the file jarfile  

