---
layout: post
title: CSS之table-layout 
date: 2013-01-30 13:24
comments: true
categories: css web layout
---

项目中有好几个页面都用到一个Table, 这个Table中有很多列，而且列数是可变化的，我们通过Javascript来动态计算出列的宽度(total_width/column_count), 但是有时候仍会出现列的宽度不一致的情况, 在W3C school了解了一下，原来有`table-layout`这个CSS属性:

table-layout有两个值可以设置(见下表)


 value        | isDefault | algorithm                                                                                  
 -----------  | --------  | ------------------------------------------------------------------------------------------ 
 auto         | YES       |  浏览器在已给定的表格/列/单元格的宽度的基础上，自己重新计算出一个最适合的宽度               
 fixed        | NO        |  禁止浏览器的重新计算行为，按用户给定的宽度显示，可能会出现单元格内容显示不完整的情况       

  
``` css
#performance_table {
    ...
    table-layout: fixed;
}
```

加上这个属性之后就OK了.

