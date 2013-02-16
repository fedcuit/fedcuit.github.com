---
layout: post
title: Linux 命令之Sort 
date: 2013-02-16 13:35
comments: true
categories: linux, unix, command 
---

Sort

* `-u` unique
* `-f` ignore case  
    fold lower case to upper case characters
* `-b` --ignore-leading-blanks   
    ignore leading blanks
* `-n` --numeric-sort  
    compare according to string numerical value
* `-t` --field-separator=SEP  
    use SEP instead of non-blank to blank transition
* `-k` --key=POS1[,POS2]  
    start a key at POS1, end it at POS2 (origin 1)


-t -k 经常配合使用
``` bash
$ cat fruit.txt
banana:30:5.5
apple:10:2.5
pear:90:2.3
orange:20:3.4
```
``` bash
$ sort -n -k 2 -t : fruit.txt
apple:10:2.5
orange:20:3.4
banana:30:5.5
pear:90:2.3
```
* `-r` --reverse  
    reverse the result of comparisons


