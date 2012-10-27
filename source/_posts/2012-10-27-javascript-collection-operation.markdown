---
layout: post
title: Javascript集合操作
date: 2012-10-27 18:12
comments: true
categories: javascript, collection
---

记录一些不常见的javascript集合操作.

#### 数组操作
``` javascript 
arr = ['a', 'b', 'c']
```
常见的遍历数组的办法如下:
``` javascript
for (var index = 0; index < arr.length; index++) { 
    console.log(arr[index]); 
}
>>a
>>b
>>c
```
我们知道在javascript中，对象跟数组都是构建在类似hashtable的数据结构上的, 即它们都是基于键值对的，所以在这一点上，我们可以把数组看成跟对象是一样的，对于一个javascript object, 我们使用 ` for (var prop in object)`这种语法来遍历一个对象中的内容，这种方式对数组一样适用:
``` javascript
for (var idx in arr) {
    console.log(arr[idx]); 
}
>>a
>>b
>>c
```


