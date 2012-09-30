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
* 解决 *Agent admitted failure to sign using the key* 问题
    * 在本机生成公钥与私钥 `ssh-keygen`
    * 将公钥(`ssh-add ~/.ssh/id_dsa.pub`)内容添加到remote machine的`~/.ssh/authorized_keys`
    * 指定本机的私钥存储位置 `ssh-add ~/.ssh/id_dsa`

##File System
* 查看文件夹大小 `du -shm`
    * -s — summarize the sizes of all available files in a folder 
    * -h — show resulted value in Human readable format 
    * -m — show the result in Megabytes

##VIM
* 保存RO文件 `:w !sudo tee %`

##Gnome
* Ubuntu下解决标题栏消失问题　`compiz-decorator --replace`
