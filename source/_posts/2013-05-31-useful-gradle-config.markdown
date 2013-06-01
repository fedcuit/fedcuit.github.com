---
layout: post
title: Useful gradle configuration
date: 2013-05-31 21:56
comments: true
categories: java build gradle
---
* Create gradle based project from template

Simply add the following to your `~/.gradle/init.gradle` script:

```groovy
  gradle.beforeProject { prj ->
    prj.apply from: 'http://www.tellurianring.com/projects/gradle-plugins/gradle-templates/apply.groovy'
  }
```

Type `gradle tasks` in any place where you want to create a gradle project, choose one from `Template Tasks`.

* Show dependencies `gradle dependencies`
* Configue dependencies using local jar files
```groovy
dependencies {
  compile fileTree (dir: 'file:c:/Middleware/modules', includes: ['*.jar'])
  compile fileTree (dir: 'file:../../../../../Libraries/trunk', includes: ['ViewController.jar'])
}
```


