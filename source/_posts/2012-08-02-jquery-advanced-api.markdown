---
layout: post
title: "jQuery高级API"
date: 2012-08-02 22:48
comments: true
categories: jQuery api 
---

**jQuery.proxy(function, context) Returns: Function**
    Takes a function and returns a new one that will always have a particular context.  

*`function`* The function whose context will be changed.  
*`context`* The object to which the context (*this*) of the function should be set.  

``` javascript using of $.proxy() in bootstrap https://github.com/fedcuit/bootstrap/blob/master/js/bootstrap-typeahead.js#L165 Source

listen: function () {
      this.$element
        .on('blur',     $.proxy(this.blur, this))
        .on('keypress', $.proxy(this.keypress, this))
        .on('keyup',    $.proxy(this.keyup, this))
      if ($.browser.webkit || $.browser.msie) {
        this.$element.on('keydown', $.proxy(this.keypress, this))
      }
      this.$menu
        .on('click', $.proxy(this.click, this))
        .on('mouseenter', 'li', $.proxy(this.mouseenter, this))
    }
```

***

**.map(callback(index, domElement)) Returns: jQuery**   
    Pass each element in the current matched set through a function, producing a new jQuery object containing the return values.  

*`callback(index, domElement)`* A function object that will be invoked for each element in the current set.

As the return value is a jQuery-wrapped array, it’s very common to  `get()` the returned object to work with a basic array.  
``` javascript using of map() in bootstrap https://github.com/fedcuit/bootstrap/blob/master/js/bootstrap-typeahead.js#L129 Source

render: function (items) {
          var that = this
            items = $(items).map(function (i, item) {
                i = $(that.options.item).attr('data-value', item)
                i.find('a').html(that.highlighter(item))
                return i[0]
              })
            items.first().addClass('active')
            this.$menu.html(items)
            return this
      }
```
`map()`功能上跟`each()`是类似的，不同之处是`each()`对elements进行操作完之后返回操作前的elements，而`map()`返回操作结果的一个集合.  

``` javascript a simpler demo on map()
var arr = ['steven', 'nicky', 'jerry', 'will'];
console.log(arr);
>>>['steven', 'nicky', 'jerry', 'will']
var arr = $(arr).map(function(index, ele){
    return ele + 1;
    }).get();
console.log(arr);
>>>["steven1", "nicky1", "jerry1", "will1"]
```

***

**jQuery.data( element, key, value ) Returns: Object**

    Store arbitrary data associated with the specified element. Returns the value that was set.
*`element`* The DOM element to associate with the data.  
*`key`* A string naming the piece of data to set.  
*`value`* The new data value.  

**Note**: This is a low-level method; a more convenient `.data()` is also available.  

The `jQuery.data()` method allows us to attach data of any type to DOM elements in a way that is safe from circular references and therefore free from memory leaks. jQuery ensures that the data is removed when DOM elements are removed via jQuery methods, and when the user leaves the page. We can set several distinct values for a single element and retrieve them later:

`data()`很容易与 `data-name`属性(html5属性)混为一谈。他们之间的关系是，如果html里面存在属性`data-name`, 那么可以通过 `data('name')`拿到这个值，仅此而已，使用`data('name2', value)`并不会添加一个 `data-name2`属性到html元素上，`data('name', value)`也不会去更新`data-name`属性的值。通过 `data(key,value)`绑定到元素的值不会显示为元素的属性.

***
**jQuery.extend( target [, object1] [, objectN] ) Returns: Object**
    Merge the contents of two or more objects together into the first object.

*`target`* An object that will receive the new properties if additional objects are passed in or that will extend the jQuery namespace if it is the sole argument.  
*`object1`* An object containing additional properties to merge in.  
*`objectN`* Additional objects containing properties to merge in.  
*`deep`* If true, the merge becomes recursive (aka. deep copy).
``` javascript using of $.extend() in bootstrap https://github.com/fedcuit/bootstrap/blob/master/js/bootstrap-typeahead.js#L29  Source
var Typeahead = function (element, options) {
    this.$element = $(element)
    this.options = $.extend({}, $.fn.typeahead.defaults, options)
    this.matcher = this.options.matcher || this.matcher
    this.sorter = this.options.sorter || this.sorter
    this.highlighter = this.options.highlighter || this.highlighter
    this.updater = this.options.updater || this.updater
    this.$menu = $(this.options.menu).appendTo('body')
    this.source = this.options.source
    this.shown = false
    this.listen()
}
```
***
**jQuery.grep( array, function(elementOfArray, indexInArray) [, invert] ) Returns: Array**  
    Finds the elements of an array which satisfy a filter function. The original array is not affected.
*`array`* The array to search through.  
*`function(elementOfArray, indexInArray)`* The function to process each item against. The first argument to the function is the item, and the second argument is the index. The function should return a Boolean value. this will be the global window object.  
*`invert`* If "invert" is false, or not provided, then the function returns an array consisting of all elements for which "callback" returns true. If "revert" is true, then the function returns an array consisting of all elements for which "callback" returns false.  
https://github.com/fedcuit/bootstrap/blob/master/js/bootstrap-typeahead.js#L90
``` javascript using of $.grep() in bootstrap https://github.com/fedcuit/bootstrap/blob/master/js/bootstrap-typeahead.js#L90 Source 
items = $.grep(this.source, function (item) {
        return that.matcher(item)
        })

```
