---
layout: post
title: jQuery change element type 
date: 2013-03-20 22:36
comments: true
categories: DOM jQuery 
---

By default, jQuery does not allow modify the `type` of a element, if you do so, a error will occur: `Error: type property can't be changed` 

``` javascript
$(':text:visible').attr('type', 'password');
```

But if when the target element is `detached` from DOM tree, then the type can be changed, so we can use jQuery to change the type in this way(we need create a new element to mark the position of the target element):

``` javascript
var $marker = $('<span />').insertBefore(':text:visible');
$(':text:visible').detach().attr('type', 'password').insertAfter($marker).focus();
$marker.remove();
```

Do you think you've get the final answer? No ! Dom element supports modify `type` directly, so the simplest way should be:

```javascript
$(':text:visible')[0].type="password"
```


