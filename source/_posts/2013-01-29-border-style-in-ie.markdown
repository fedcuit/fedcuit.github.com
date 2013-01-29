---
layout: post
title: Border 属性在IE下的奇怪表现
date: 2013-01-29 13:19
comments: true
categories: css style web front
---
今天在调前端的时候遇到一个问题，有一个TABLE中的TR在IE下总是多显示一条很粗的底边框出来，
查看CSS时看到边框的样式是由下面的代码来控制的，似乎是为了强制在IE下显示solid的边框而非虚线.
``` css
table {
    td, th {
        // IE's dotted lines look bad
        border-style: solid !important;
    }
}
```
由于打算修改这段CSS让其不显示下边框,
``` css
table {
    td, th {
        // IE's dotted lines look bad
        border-style: solid !important;
        border-bottom: none !important;
    }
}
```
刷新页面后还是没有起作用，Google后发现原来在IE下面对 `border-bottom` 这种简写形式支持的不好，需要拆分成完整形式，
``` css
table {
    td, th {
        // IE's dotted lines look bad
        border-style: solid !important;
        border-bottom-style: none !important;
        border-bottom-width: 0px;
    }
}
```

午饭后又去W3C看了一下CSS border相关的reference, 得出 `border` 由三个属性组成: 

* `width`
* `style`
* `color`

我们可以通过`border`一次性指定`width`, `style`, `color`, 也可以通过 

* `border-width`
* `border-style`
* `border-color`

来分别指定属性.

See more [HERE](http://www.w3school.com.cn/css/css_border.asp)


