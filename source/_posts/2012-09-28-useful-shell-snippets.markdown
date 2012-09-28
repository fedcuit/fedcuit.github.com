---
layout: post
title: 实用Shell脚本片段
date: 2012-09-28 22:22
comments: true
categories: linux, unit, shell, script
---

##### 条件判断
* `[ -d ~/directory ] || mkdir ~/directory` 如果目录不存在，则创建此目录
* `[ -f ~/file ] || touch ~/file` 如果文件不存在，则创建此文件

***

##### 文件操作 
* `echo "some content" >> ~/file` 向文件末尾添加内容
* `sed -i .bak 's/old/new/g' file` 首先生成file的备份文件file.bak, 然后将file文件中出现的所有old替换成new

***

##### 函数操作
* 函数的定义与调用
风格上与Javascript相似，但是函数的定义与声明都不需要加括号，**shell函数不使用括号** 
```bash
function main {
    echo "in main function"
}
main
```

* 参数的传递
Shell函数没有参数列表这个概念, 在函数内部通过$n来接受第n个位置上的参数
```bash
function greeting {
    echo "Hello, $1, your id is $2" 
}

function main {
    greeting "Feng Erdong" edfeng
    greeting "Zhang xiaoming" xmzhang
}

main

```
