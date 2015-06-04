---
layout: post
title: "Backbone体验 -- Model篇"
date: 2015-06-04 08:52
comments: true
categories: js javascript backbone model
---

Model
-------

在`Backbone`的理念中, 一个`Model`应该算是存在于客户端的与服器端`resource`/`entity`对应的一个JS对象, 定义一个`Model`很简单:
``` javascript
TodoItem = Backbone.Model.extend({
    defaults: {
        title: '',
        completed: false
    },
    validate: function (attributes) {
      // validation goes here
    }
})
```
创建一个`Model`的实例就是创建一个对象 `var todoItem = new TodoItem()`, 下面对定义在`Model`中的常用属性做一下说明(_这些属性其实是一些钩子, `Backbone`希望我们去重写这些属性_)

* `defaults`

  通过这个属性来指定`Model`的默认值

  当创建Model实例的时候没有传入对象来初始化, 那么这个实例的属性就与`defaults`指定的属性值是一致的, 如:
``` javascript
var todoItem = new TodoItem();
console.log(JSON.stringify(todoItem.toJSON()));
// {"title":"","completed":false}
```
  当创建Model实例时传入了对象, 那么这个对象会跟`defaults`进行merge
``` javascript
var todoItem = new TodoItem({"newAttr": "valueOfNewAttr", "title": "Build a time machine"});
console.log(JSON.stringify(todoItem.toJSON()));
// {"newAttr":"valueOfNewAttr","title":"Build a time machine","completed":false}
```

* `validate`

   将对`Model`的验证逻辑放在这个属性对应的方法中, 一般情况下, 我们不会直接调用这个`validate`方法, 它扮演的角色有点像`实现`定义在父类中的`抽象方法`, 这个方法会在`Model`的其他方法中被用到, 比如以下的这些方法:

   `isValid()`

   我们可以调用`Model`上的`isValid()`方法来判断这个`Model`上的属性值满足验证条件

```javascript
TodoItem = Backbone.Model.extend({
defaults: {
    title: '',
    completed: false
},
validate: function (attributes, options) {
    if (attributes.title.indexOf("<") != -1) {
        return "html tag is now allowed is title"
    }
}
});
var todoItem = new TodoItem({
    "title": "<script>...</script>"
});
console.log(todoItem.isValid());
// false
```
   `save()` or `set()`

   默认情况下调用`Model`的`save()`方法会触发`validate()`, 可以通过在`save()`的时候使用option `{validate: false}`来跳过验证. 类似的, 也可以在`set()`时通过option `{validate: true}`来触发验证.

   `validate()`方法的返回值比较有趣, 如果验证通过了, 则什么都不用返回, 如果验证失败了, 则需要返回点什么(字符中或是对象都可以).

   `validate()`方法验证失败之后有两件事件会发生:

   * 将`validate()`返回的结果添加到`Model`中, 可以通过`model.validationError`来访问
   * 在`Model`上触发`invalid`事件, 并像model和error绑定对回调函数上
