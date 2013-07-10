---
layout: post
title: Testing Using Gradle
date: 2013-07-07 19:29
comments: true
categories: java test gradle
---

* Run a single unit test

```groovy
gradle test -Dtest.single=TestClassName
```
* Run test in debug mode
```groovy
gradle test -Dtest.debug
```
