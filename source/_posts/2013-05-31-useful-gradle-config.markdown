---
layout: post
title: Useful gradle configuration
date: 2013-05-31 21:56
comments: true
categories: java build gradle
---

* Show dependencies `gradle dependencies`
* Configue dependencies using local jar files
```groovy
dependencies {
  compile fileTree (dir: 'file:c:/Middleware/modules', includes: ['*.jar'])
  compile fileTree (dir: 'file:../../../../../Libraries/trunk', includes: ['ViewController.jar'])
}
```


