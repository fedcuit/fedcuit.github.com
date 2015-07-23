---
layout: post
title: JSX里的那些个坑
date: 2015-07-23 21:52
comments: true
categories: jsx react
---

最近在项目上使用`Backbone` + `React`作为前端框架, Backbone主要是用来作为`Model`来与后台的Restful API进行交互, React则负责`View`, 也算是发挥了各自的优势: Backbone的Model和Collection只需要指定对应Resource的url, 就可以直接读取/更新/删除记录, 而不需要手动去发送对应的AJAX请求, 另外借助于寄生在Model/Collection上的`underscore`方法, 操作数据也很方便; React接收Backbone的Model/Collection作为`state`或`props`, 通过`Virtual DOM`来高效的DOM更新, 与其他的View技术相比较, React功能更单一, 概念更少更易理解, 而且有更高的性能. 这一切看下来都是那么的美好, 但是使用React还是在它的代价的, 代价就是`JSX`.

简单讲, `JSX`也就是一段写在JavaScript中的XML, 它是由React提供的一种更简洁的生成Virtual DOM的方法, 通过使用`JSX`我们可以像书写`HTML`一样来编写React Component, 然后再使用对应的编译器将`JSX`定义的Component Tree_翻译_成不易读的`React.createElement(...)` (Virtual DOM就是由`React.createElemnt()`创建出来的)

正是因为`JSX`代码会最终被翻译成对应的Javascript代码, 所以在书写`JSX`代码的时候, 虽然我们说他的形式像是`HTML`一样, 但是我们并不能完全遵照HTML规范来书写, 在`JSX`中使用HTML的属性还是有一些限制(坑)的:

避免出现Javascript关键字
----

因为`JSX`会被编译成JS代码供浏览器执行, 所以如果在`JSX`中使用了Javascript的关键字的话, 那么很可能导致生成的代码无法被正确解释.(在IE上这个问题格外严重, 但在强大的Chrome上, 这种情况可以被很智能地处理).

一看下面的例子就明白了:

这段`JSX`代码生成一个表单, 其中有一个Label和对应的Input用来输入用户的名字,

```html
<form>
   <label for="name">Your name:</label>
   <input type="text"/>
</form>
```

根据它编译生成的Javascript代码长这个样子:

```javascript
React.createElement("form", null,
    React.createElement("label", {
        for: "name"
    }, "Your name:"),
    React.createElement("input", {
        type: "text"
    })
);
```

通过比较我们基本上可以总结出这个_翻译_的过程, 对于一个`JSX Tag`:
```html
<tagName attr1=val1 attr2=val2>
  [childrenComponents]
</tagName>
```
它生成的Javascript应该是:
```javascript
React.createElement("tagName", {attr1: val1, attr2: val2}, [childrenReactComponents]);
```

可以看出其中`TagName`是最安全的, 因为它在生成的JS中是一个字符串, 但是HTML Attribute就没有这么幸运了, 如果它碰巧是一个Javascript关键字, 那么恭喜你, 你掉到坑里去了. 比如上例中`label`上面的`for`属性在被编译后的Javascript代码中就很可能被浏览器当作是*循环*的那个for, 从而报出一个语法错误出来.

为了解决类似这种问题, React为这些"不幸"的属性定义了对应的"替身", 它们中的一些是(不完全版):


Native HTML Attribute | React Version 
:-------------------- | :------------
`for`                 | `htmlFor`       


(未完待续...)
