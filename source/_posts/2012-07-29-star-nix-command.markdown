---
layout: post
title: Shell命令收集
date: 2012-07-29 18:54
comments: true
categories: unix linux command shell
---

##User Management
* Add group `sudo groupadd groupname`
* Add user and specify group and home folder `sudo useradd username -g groupname -d path_to_home_folder`
* Add user home folder `mkdir /path/to/home_folder` && `sudo chmod -R groupname:username /path/to/home_folder`
* Set password for user `sudo passwd username`
* Add existing user to exising group `sudo usermod -a -G groupname username`
* Check user information `id` will show up `username`, `primary group name` and `all group names`
* Switch user `su [username]`
    if no username specified, default switch to supersuer
* Switch user and load simimar environment as if it's a direct user login `su - username`
* Add user to sudoer list `sudo adduser username sudo`

## Terminal/Console

* Delete one words before cursor `<CTRL+w>`
* Delete all words after cursor `<CTRL+k>`
* Move to head of line `<CTRL+a>`
* Move to end of line `<CTRL+e>`
* Clean line `<CTRL+u>`
* Clean screen `<CTRL+l>`

* Paste last parameter of last command `<ESC+.>`

* Generate random number `echo $RANDOM`
* Show full path `ls -d $PWD/*`


##Network
* MAC refresh DNS `sudo dscacheutil -flushcache`
* Windows refresh DNS `ipconfig /flushdns`
* Linux refresh DNS `sudo /etc/init.d/networking restart`
* show public IP `curl ifconfig.me`
* show local DNS `cat /etc/resolv.conf`
* Resolve *Agent admitted failure to sign using the key*
    * generate private key and public key on machine `ssh-keygen`
    * (`ssh-add ~/.ssh/id_dsa.pub`) add content of local public key to remote machine's `~/.ssh/authorized_keys`
    * specify location of private key on local machine `ssh-add ~/.ssh/id_dsa`

##File System
* Check total size of a folder  `du -shm`
    * -s — summarize the sizes of all available files in a folder
    * -h — show resulted value in Human readable format
    * -m — show the result in Megabytes

##VIM
* save read-only file `:w !sudo tee %`

* Change to uppercase `visual + U`
* Change to lowercase `visual + u`
* Toggle case `visual + ~`

##Gnome
* Fix the problem that title bar dispear on ubuntu　`compiz-decorator --replace`

##File Operation
* batch move files `mv src_folder1 src_folder2 src_folder3 -t dest_folder`
