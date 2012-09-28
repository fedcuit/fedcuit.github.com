---
layout: post
title: "jQuery事件处理杂记"
date: 2012-08-04 12:30
comments: true
categories: jQuery event 
---

* 在`.on()`中判断事件源  
    jQuery的事件处理函数中默认存在`event`这个对象， 在使用`on` 这种事件委托机制时，有时候我们想监听的特定元素的事件在收到之前就已经被其他外层的元素捕获到了，这个时候可以通过`event.target.nodeName`来拿到真正的事件源的结点名。注意，结点名是大写的.

``` javascript a simple demo may be not that reasonable
$("#resultTable")
//show edit form when click on tr
.on("click", "tr", showUpdateEventForm)
//enable/disable toolbar button when click inner checkbox of tr
.on("click", "tr", function(event){
        var isCheckboxClicked = event.target.nodeName == 'INPUT';
        if (!isCheckboxClicked) {
            var $checkbox = $(this).find(':checkbox');
            $checkbox.attr('checked', !$checkbox.attr('checked'));
        }
        $toolbarButtons.refresh();
    })
```

* 建议在`.on()`的事件处理函数中显式接受event对象  
    在chrome中，我们可以直接在`on()`指定的事件处理函数中使用`event`对象而不用显式传递这个`event`,但是在Firefox下必须显式传递这个event对象.

* 使用事件委托避免inline事件注册  
     当使用requireJS, text来异步加载html片段到页面时，这个时间整个页面已经加载完毕，因为无法再给这段html片段中的元素添加事件监听，如果想在这段html片段中使用inline javascript来注册事件的话，又需要使用全局的javascript对象，这违背了使用requireJS来管理javascript的初衷。  
     一种比较好的解决办法是，在这段html片段的父元素上使用事件委托机制来监听他的子元素上发生的事件，这样即使这段html片段是后来加入到页面的，也完全可以在$()中完成事件注册.



