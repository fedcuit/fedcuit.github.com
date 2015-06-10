---
layout: post
title: Call vs Apply vs Bind
date: 2015-06-10 11:29
comments: true
categories: javascript js function
---
在JavaScript中, 函数是一等公民, 可以作为参数传入另一个函数, 也可以作为函数从另一个返回中返回, 不仅如此, 在JavaScript这门动态语言中, 函数可以被任意对象调用, 比如说`function1`是定义在`obj1`上的, 但是它还可以被`object2`所调用, 怎么做到的? 有下面的三种办法:

* **Call**

基本语法: `fnc.call(thisArg, arg1, arg2... argN)`

 * `thisArg`将作为`this`对象传入到函数`fnc`中
 * `arg1, arg2... argN`作为参数列表传入到函数中
	
通过`call`方法可以让一个函数被任何的对象所使用, `call()`会立即返回使用`thisArg`作为`this`对象, 余下部分作为参数列表的调用函数`fnc`的结果, 如:

```javascript
var person = {
  toString: function(delimiter) {
    return "height=" + this.height + delimiter + " weight=" + this.weight;
  }
};

person.height = "180";
person.weight = "80";

console.log(person.toString(","));
//"height=180, weight=80"

var dog = {};
dog.height = "40";
dog.weight = "30";

console.log(person.toString.call(dog, ":"));
//"height=40: weight=30"
```
* **Apply**

上面的例子里`toString`只是返回了身高与体重的数值, 但是只有数据是没有意义的, 我们还需要单位, 出于演示的目的我们打算将单位传为参数传入`toString`方法, 修改后的代码如下:
```javascript
var person = {
  toString: function(delimiter, heightUnit, weightUnit) {
    return "height=" + this.height + heightUnit + delimiter + " weight=" + this.weight + weightUnit;
  }
};

person.height = "180";
person.weight = "80";

console.log(person.toString(",", "cm", "kg"));
//"height=180cm, weight=80kg"

var dog = {};
dog.height = "40";
dog.weight = "30";

console.log(person.toString.call(dog, ":", "cm", "kg"));
//"height=40cm: weight=30kg"
```
现在在调用`call`的时候需要传入三个参数(除去thisArg),  还不错, 不过接下来我们打算将更进一步, 我们希望在dog对象上添加一个方法`justDoIt`, 不管这个`justDoIt`接收什么参数, 都要将对`justDoIt`函数的调用代码到`toString`方法上去, 先试试看`call`能不能帮我们解决这个问题:
```javascript
var person = {
  toString: function(delimiter, heightUnit, weightUnit) {
    return "height=" + this.height + heightUnit + delimiter + " weight=" + this.weight + weightUnit;
  }
};

person.height = "180";
person.weight = "80";

console.log(person.toString(",", "cm", "kg"));
//"height=180cm, weight=80kg"

var dog = {
  justDoIt: function() {
    person.toString.call(this, arguments);
  }
};
dog.height = "40";
dog.weight = "30";

console.log(dog.justDoIt(":", "cm", "kg"));
//"height=40undefined[object Arguments] weight=30undefined"
```
可以看到`arguments`这个数组被赋值给了第一个参数`delimiter`, 而`heightUnit`, `weightUnit`都没有被正确的赋值, 所以他们都是`undefined`.

要解决上面的问题, 就需要`apply`出场了, `apply`与`call`非常的相似, 唯一的区别就是 `call`需要明确地为每一个参数传值, 而`apply`只需要一个参数数组就可以了, 当原函数被调用的时候, `apply`会将这个参数数组`unpack`并给对应位置上的参数赋值.
```javascript
var person = {
  toString: function(delimiter, heightUnit, weightUnit) {
    return "height=" + this.height + heightUnit + delimiter + " weight=" + this.weight + weightUnit;
  }
};

person.height = "180";
person.weight = "80";

console.log(person.toString(",", "cm", "kg"));
//"height=180cm, weight=80kg"

var dog = {
  justDoIt: function() {
    return person.toString.apply(this, arguments);
  }
};
dog.height = "40";
dog.weight = "30";

console.log(dog.justDoIt(":", "cm", "kg"));
//"height=40cm: weight=30kg"
```
* **Bind**