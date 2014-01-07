---
layout: post
title: Master AngularJS - directives
date: 2014-01-07 20:12
comments: true
categories: angularjs javascript
---

I've been using angularjs for several months, at the beginning, i always think everything in jQuery way, since I've used jQuery heavily on all of my previous projects, but day by day
I became like angularjs, i realize the shortcoming of jQuery: you will not know who is responsile for the event happens on this element until you see the event binding in jQuery code;
lots of manipulation is based on css selector which is fragile; it's also hard and tricky to transfer data between different element ...

So I decide to write a series of articles to record what I've learned in the past months, these articles are not for beginners, I assume you have a basic concept with AngularJS, but still don't know 
how to write AngularJS in the right way. Let's start with **directives** .

## Directive Definition Object

A directive usually appears as a element/tag name or attribute, it's used to add additional functionality to the element, like, `<a toggle-background/>`, the directive toggleBackground will switch the 
background color of the current page once you click it, it's more clear than implement the same logic with jQuery, you will understand what will happen when you see the directive on the link.

Let's take a closer look to directives, below is a simple directive defination.
```javascript
angular.module("app",[]) .directive("alert",[function(){
    return {
      restrict: 'EA',
      link: function(scope, elm, attr){
      }
  };
  }]);
```
## Link Function
we can ignore the `restrict` property, by default a directive can appear as a attribute, let's goes into the most important part in a directive: the **link function** .

The link function take three parameters: `scope`, `elm`, `attr`, the last two are easy to understand:

* *`elm`* the jQuery object representation of the element which this directve blongs to

* *`attr`* all the attributes on this element, it's a map of attribute name and value, given `<a type="text" some-directive/>`, `attr.type` will return `text`

## (Isolated) Scope
Why we need scope for directive ? First, let's devide all kinds of directves into two groups:

* *`without scope`*
  If what we do in this the directive is only DOM manipulation, then `elm` and `attr` is enough for use, we don't need to declare a isolated scope.

* *`with scope`*
  If we need to manipulate angular model in directive, then we need to declare a scope.


To be continue...

