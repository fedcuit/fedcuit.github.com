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
`class`               | `className`       



另类的inlineStyle
----

inline style估计是最让React的初学者头痛的问题了, 如果我们按照HTML规范来使用的话, 是无法将想要的样式apply到元素上的, 至于为什么, 我们看一下由JSX编译成的JS代码就明白了:

```html
<div style="color: yellow">Hello World</div>
```
我们在JSX中定义了一个DIV元素, 并且想通过inline style将它里面的文字颜色设为*yellow*, 然而这个DIV甚至都无法被渲染到页面中, 因为React认为它是不合法的:

```javascript
React.createElement("div", {style: "color: yellow"}, "Hello World");
```
在上面这段生成的JS里面, 用于设置`style`的值是一个字符串, 这显然是无法工作的, 因为`style`这个属性与其他属性不一样, 它比较特殊, 一般的属性的值都是一个普通的字符串, 比如`type="text"`, `name="fieldName"`, 这些属性值都是可以被浏览器直接使用的, 而style它传递的是一组样式属性, 它在这里已经不是一般的字符串, 而是可以被解析的一个样式对象, 说到这里, 估计你也猜到了, 我们不能直接使用一个普通字符串给style, 我们要传递的应该是一个JS对象(JSON)才对:
```html
<div style="{color: yellow}">Hello World</div>
```
但是将代码改成这样之后还是无法将这个DIV渲染出来, 再次查看生成的JS代码:

```javascript
React.createElement("div", {style: "{color: yellow}"}, "Hello World");
```
style的值还是一个字符串! 这里正好引出了JSX的一条很重要的规则, 即被放在`{}`中的都作为Javascript代码来处理, 但是如果`{}`被引号包围起来, 那么它当作一个字符串处理. 这可能是JSX让人感觉比较困惑的地方, 当在JSX中使用`{}`来引用JS变量的时候, 很容易让人感觉`JSX`似乎是模板引擎, 而对于模板引擎来说(比如Freemarker, JSP等), 它们会将所模板中所有的变量都替换成对应的值, 不管它们是不是在一对双引号中:

如在Freemarker中, 如果变量 `userName`的值是 'Erdong FENG', 使用的模板是
```html
<div data-name="${userName}">Welcome to our website</div>
```
那么最终生成的文件是这样的:
```html
<div data-name="Erdong FENG">Welcome to our website</div>
```

然而在JSX中并不是这样的, 因为*`JSX不是模板引擎`* (当然了, 大多数时候JSX表现的还是像一个模板引擎, 比如在一个DIV的文字结点中, 但是在一个Tag的属性值中使用变量的时候, 情况完全不一样)
```html
<div data-name="{userName}">Welcome to our website</div>
```
可以看到, 当你将变量名作为属性值的时候, 而且给这个属性值加上了引号的话, 它会被当作一个字符串被处理:
```javascript
React.createElement("div", {"data-name": "{userName}"}, "Welcome to our website");
```
```html
<div data-name="{userName}">Welcome to our website</div>
```
当然了, 我们在innerHTML的位置还是可以将JSX当作模板引擎来使用的:
```html
<div>Welcome to our website, "{userName}"</div>
```
最终的JS跟HTML如下:
```javascript
React.createElement("div", null, "Welcome to our website, \"", userName, "\"");
```
```html
<div>
    <span>Welcome to our website, "</span>
    <span>Erdong FENG</span>
    <span>"</span>
</div>
```
一下子跑得有点偏了, 再回顾一下, 在JSX中, `{}`中的东西是当作JS来处理的, 因为在生成的HTML中这部分会被对应的JS表达式的值替换, 但是如果是在属性值中使用`{}`, 那么就一定不能用引号将它括起来了, 不然的话它就会当作字符串来处理!

再回到inline style的问题上, 基于上面的分析, 我们这次不给属性值加引号了
```html
<div style={color: yellow}>Welcome to our website</div>;
```
再编译一次, 这次直接就语法错误了, 因为`{}`里面的部分是被当作Javscript来处理的, 而`color: yellow`根本不是合法的JS语句, 再回顾一下, 我们这里需要的属性值是一个JSON, 那么应该是`{color: yellow}`才对, 再将这个JSON放到`{}`中去:
```html
<div style={ {color: yellow} }>Welcome to our website</div>
```
```javascript
React.createElement("div", {style: {color: yellow}}, "Welcome to our website");
```
怎么还是有问题?! 原来是因为yellow被当作一个JS变量来解析, 但是却没有对应的变量存在, 所以这时候又引出了在JSX中使用inline style的另一条规则, 对于这些非字面量的值, 需要将他们用引号括起来, 表示他们是字符串:
```html
<div style={ {color: 'yellow'} }>Welcome to our website</div>
```
这样就可以了!

除此之外, 还有另一个需要注意的地方, 如果style的属性名中含有连字符`-`, 也需要做适当转换的, 因为属性名在编译后的JS代码中是JSON中的key, key要适合JS的变量名的定义, 因此是不能使用连字符`-`的, React的解决办法是使用它的camelPattern, 好的, 讲完了.
```html
<div style={ {color: 'yellow', backgroundColor: 'green'} }>Welcome to our website</div>
```
