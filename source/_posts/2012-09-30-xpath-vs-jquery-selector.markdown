---
layout: post
title: Xpath 与 jQuery selector 的对应关系 
date: 2012-09-30 17:00
comments: true
categories: xpath jquery css selector
---
* *如果默认是从根找起的话, xpath需要以`//`开头*
* *如果使用属性选择器, jQuery的属性值可以不使用引号，但是xpath中属性值必须用引号括起来*

## 基本选择器
* **id**
    * `$('#import-datasenders')`
    * `$x('//a[@id="import-datasenders"]') `
* **class**
    * `$('a.register_data_sender')`
    * `$x('//a[@class="register_data_sender"]')` 
* **tag**
    * `$('a')`
    * `$x('//a')`
* __*__
    * `$('#import-datasenders')`
    * `$x('//*[@id="import-datasenders"]') `
* **选择器组**
    * `$('#import-datasenders, .register_data_sender')`
    * `$x('//*[@id="import-datasenders"]|//*[@class="register_data_sender"]')`

## 层次选择器
* **后代元素**
  * `$('#my_subjects li')`
  * `$x('//*[@id="my_subjects"]//li')`
* **直接子元素**
  * `$('.secondary_tab>li')`
  * `$x('//*[@class="secondary_tab"]/li')`
* **下一个兄弟元素**
  * 
    1. `$('input+image')` 
    2. `$('input').next()`

  * `$x('//input/following-sibling::img')` 

* **上一个兄弟结点**
  * `$('input').prev()`
  * `$x('//input/preceding-sibling::*')`

  xpath的这种写法与上面的jQuery写法非常的相似，但是功能并不完全相同. jQuery 会取当前元素的 __紧跟着的__ 下一个/上一个 符合条件的兄弟结点, 而xpath会从当前元素的所有兄弟结点中查找符合条件的，但是不保证这个兄弟结点是当前结点的下一个/上一个结点.

* **之后的所有兄弟结点**
  * 
    1. `$('input~image')` 
    2. `$('input').nextAll()` 
  * `$x('//input/following-sibling::img')` 

* **之前的所有兄弟结点**
    * `$('input').prevAll()`
    * `$x('//input/preceding-sibling::*')`
* **所有的兄弟结点**
  * `$('input').siblings()`
  * `$x('//input/following-sibling::*|//input/preceding-sibling::*')`

##过滤选择器
###基本过滤选择器
* **:first**
  * `$('ul>li:first')`
  * `$x('//ul/li')[0]`

* **:last**
  * `$('a:last')`
  * `$x('//a').pop(-1)`

  jQuery伪类`:first`/`:last`返回的是结果集里面的 第一个/最后一个 元素，这已经与css selector无关了，完全是对数组的操作.


* **:even** 

* **:odd**

* **:eq(index)**

* **:lt(index)**

* **:gt(index)**

  上面这些选择器都是jQuery特有的，他们完全是为了简化对结果集的数组操作.



###内容过滤选择器
* **:contains(text)**
  * `$('ul>li:contains("Download")')`
  * `$x('//ul/li[contains(text(), "Download")]')`

* **:empty**
  * `$('a:empty')`
  * ?

* **:parent**
  * `$('td:parent')`
  * `$x('//text()/parent::td | //*[not(text())]/parent::td')`

  
* **:has(selector)**
  * `$('ul:has(a)')`
  * `$x('//a/ancestor::ul')`

###可见性过滤器
* **:hidden**
  * `$('input:hidden')`
  * ?
* **:visible**
  * `$('input:visible')`
  * ?

###属性过滤器
* **[attribute]**
  * `$('div[style]')`
  * `$x('//div[@style]')`

* **[attribute=value]**
  * `$('a[target=_blank]')`
  * `$x('//a[@target="_blank"]')`

* **[attribute!=value]**
  * `$(':text[name!=email]')`
  *  
      * `$x('//input[@type="text"][@name!="email"]')`
      * `$x('//input[@type="text" and @name!="email"]')`

* [attribute^^=value]
  * `$('input[name^=tel]')`
  * `$x('//input[starts-with(@name, "tel")]')`

* **[attribute$=value]**
  * `$('input[name$=number]')`
  * `$x('//input[ends-with(@name, "num")]')`

  `ends-with`这个function并没有在 chrome console 跟 firebug console中测试通过，个人估计可能是因为xpath的版本问题.
  
* **[attribute*=value]**
  * `$('input[name*=tel]')`
  * `$x('//input[contains(@name, "tel")]')`

* **[att1][att2][att3]**
  * `$('input[type=text][name*=tel]')`
  * `$x('//input[@type="text"][contains(@name, "tel")]')`

###子元素过滤器

* **:nth-child**
    * `$('ul>li:nth-child(1)')`
    * `$x('//ul/li[position()=1]')`

 xpath中的`position()`函数与jQuery的`:nth-child`伪选择器功能完全相同，而且都是base 0的.

* **:first-child**
  * `$('ul>li:first-child')`
  * `$x('//ul/li[position()=1]')`

* **:last-child**
  * `$('ul>li:last-child')`
  * `$x('//ul/li[last()]')`

  xpath中貌似没有`first()`函数

