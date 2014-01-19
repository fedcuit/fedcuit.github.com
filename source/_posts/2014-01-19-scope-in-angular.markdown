---
layout: post
title: Scope In Angular
date: 2014-01-19 12:53
comments: true
categories: javascript angular scope
---
This post is copied from stackvoerflow, check [this](http://stackoverflow.com/questions/14914213/when-writing-a-directive-how-do-i-decide-if-a-need-no-new-scope-a-new-child-sc) for more details.


After writing a lot of directives, I've decided to **use less isolated scope**. Even though it is cool and you encapsulate the data and be sure not to leak data to the parent scope, it severely limits the amount of directives you can use together. So,

## Isolated: a private sandbox
If the directive you're going to write is going to behave entirely on its own and you are not going to share it with other directives, go for isolated scope. (like a component you can just plug it in, with not much customization for the end developer) (it gets very trickier when you try to write sub-elements which have directives within)
## None: simple, read-only directives
If the directive you're going to write is going to just make dom manipulations which has needs no internal state of scope, or explicit scope alterations (mostly very simple things); go for no new scope. (such as ngShow,ngMouseHover, ngClick, ngRepeat)
## Child: a subsection of content
If the directive you're going to write needs to change some elements in parent scope, but also needs to handle some internal state, go for new child scope. (such as ngController) 

So keep in mind that don't use isolated scope unless you have to.
