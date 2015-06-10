---
layout: post
title: Call vs Apply vs Bind
date: 2015-06-10 11:29
comments: true
categories: javascript js function
---
在JavaScript中, 函数是一等公民, 可以作为参数传入另一个函数, 也可以作为返回值从另一个函数中返回, 不仅如此, 在JavaScript这门动态语言中, 函数可以被任意对象调用, 比如说`function1`是定义在`obj1`上的, 但是它还可以被`obj2`所调用, 怎么做到的? 有下面的三种办法:

* **Call**

基本语法: `fnc.call(thisArg, arg1, arg2... argN)`

 * `thisArg`将作为`this`对象传入到函数`fnc`中
 * `arg1, arg2... argN`作为参数列表传入到函数中
	
通过`call`方法可以让一个函数被任何的对象所使用, `call()`会立即返回使用`thisArg`作为`this`对象, 余下部分作为参数列表来调用原函数`fnc`的结果, 如:

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

上面的例子里`toString`只是返回了身高与体重的数值, 但是只有数值是没有意义的, 我们还需要单位, 出于演示的目的我们打算将单位传为参数传入`toString`方法, 修改后的代码如下:
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
现在在调用`call`的时候需要传入三个参数(除去thisArg不算),  还不错, 不过接下来我们打算将更进一步, 我们希望在dog对象上添加一个方法`justDoIt`, 不管这个`justDoIt`接收什么参数, 都要将对`justDoIt`函数的调用代理到`toString`方法上去, 先试试看`call`能不能帮我们解决这个问题:
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

要解决上面的问题, 就需要`apply`出场了, `apply`与`call`非常的相似, 唯一的区别就是`call`需要明确地为每一个参数传值, 而`apply`只需要一个参数数组就可以了, 当原函数被调用的时候, `apply`会将这个参数数组`unpack`并给对应位置上的参数赋值.
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

如果你对函数式编程有所了解, 那么你应该知道`柯里化(currying function)`与`偏函数(partial function)`, 它们有个共同之处就是都是从一个函数上产生出的另一个新的函数. 

偏函数可以视为是针对某种情况简化之后的函数, 比如说现在有一个函数`awesomeFunction(arg1, arg2, arg3)`, 而对这个函数的调用过程中, 第一个参数几乎都是相同的, 作为眼睛里容不得重复的程序员, 我们应该有一种办法可以消除这种重复, 这个时候偏函数就伴着掌声入场了, 我们在偏函数的声明中已经预置了第一个参数`arg1=mostFrequentlyUsedValue`, 这样我们调用偏参数的时候只需要传入`(arg2, arg3`)就可以了.

那么现在问题来了, JavaScript中怎么实现偏函数呢?

这时候就需要使用`bind`了, `bind`的基本语法与`call`一致:
`fnc.bind(thisArg, arg1, arg2... argN)`

但是它们有一个重要的区别, 那就是`bind`不会立即执行原函数, 而是返回一个新的函数, 并且这个新的函数在被调用的时候会自动将已经`bind`上去的那些参数值赋给对应位置的上的参数, 新的函数只需要关心余下的那部分参数列表.
```javascript
function greet(timeFrame, msg) {
  return "Good " + timeFrame + ", " + msg;
}

console.log(greet("Morning", "What can I do for you?"));
console.log(greet("Morning", "This is your ticket"));
console.log(greet("Morning", "welcome back"));
console.log(greet("Morning", "How was the trip"));
console.log(greet("Afternoon", "Can I borrow you some time?"));
// "Good Morning, What can I do for you?"
// "Good Morning, This is your ticket"
// "Good Morning, welcome back"
// "Good Morning, How was the trip"
// "Good Afternoon, Can I borrow you some time?"
```
上面的例子中`greet`这个函数多数情况下都是使用"Morning"作为第一个参数, 何不创建一个更简短的函数来专门处理这种场景呢:
```javascript
function greet(timeFrame, msg) {
  return "Good " + timeFrame + ", " + msg;
}

var morningGreet = greet.bind(undefined, "Morning");

console.log(morningGreet("What can I do for you?"));
console.log(morningGreet("This is your ticket"));
console.log(morningGreet("welcome back"));
console.log(morningGreet("How was the trip"));
console.log(greet("Afternoon", "Can I borrow you some time?"));
```

了解了`bind`其实是返回一个新的函数之后, 那么其实想通过它来实现上面`call`/`apply`的功能其实就非常简单了, 只需要调用一下生成的新的函数就可以了.
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

console.log(person.toString.bind(dog, ":")());
//"height=40: weight=30"
```
---

上面只是从函数可以被任意对象调用的角度来分析了`call`, `apply`, `bind`的用法, 其实实际使用的过程中, 这些方法还有另一个非常重要的用途, 那就是给回调函数绑定正确的上下文环境(this对象).