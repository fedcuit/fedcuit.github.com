---
layout: post
title: Shell命令收集  
date: 2012-07-29 18:54
comments: true
categories: unix linux command shell 
---
## Terminal/Console

* 删除光标之后的字符 `<CTRL+k>`
* 移动光标到行首 `<CTRL+a>`
* 移动光标到行尾 `<CTRL+e>`
* 清除整行 `<CTRL+u>`
* 清除屏幕 `<CTRL+l>`

* 产生一个随机数 `echo $RANDOM`
* 显示完整路径 `ls -d $PWD/*`


##Network
* MAC 刷新DNS `sudo dscacheutil -flushcache`
* Windows 刷新DNS `ipconfig /flushdns`
* Linux 刷新DNS `sudo /etc/init.d/networking restart`
* 获取公网IP `curl ifconfig.me`
* 查看本地DNS `cat /etc/resolv.conf`


##VIM
* 保存RO文件 `:w !sudo tee %`

##Gnome
* Ubuntu下解决标题栏消失问题　`compiz-decorator --replace`
