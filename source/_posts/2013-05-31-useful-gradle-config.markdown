---
layout: post
title: Useful gradle configuration
date: 2013-05-31 21:56
comments: true
categories: java build gradle
---
#Basic configuration
* Must have: `java plugin`, `maven repo`, `dependencies`
```groovy
apply plugin: 'java'

repositories {
    mavenCentral()
}

dependencies {
  testCompile 'info.cukes:cucumber-java:1.1.3'\
    , 'info.cukes:cucumber-junit:1.1.3'\
    , 'junit:junit:4.11'\
    , 'org.seleniumhq.selenium:selenium-java:2.32.0'\
    , 'org.easytesting:fest-assert:1.4'
}

```

* Enable to run cucumber (with selenium)
```groovy
configurations {
  cucumberRuntime {
    extendsFrom testRuntime
  }
}

task cucumber() {
  dependsOn assemble, compileTestJava
    doLast {
      javaexec {
        main = "cucumber.api.cli.Main"
          jvmArgs = ["-Dwebdriver.chrome.driver=/Users/edfeng/Downloads/chromedriver"]
          //Must set if you want to use chrome webdriver
          classpath = configurations.cucumberRuntime + sourceSets.main.output + sourceSets.test.output
          args = ["-g", "gradle.cucumber", "src/test/resources"]
          // 1. must set package name of glue code(where steps are defined). 2. must set directory of feature files.
      }
    }
}


```

#Advanced configuration
* Create gradle based project from template

Simply add the following to your `~/.gradle/init.gradle` script:

```groovy
  gradle.beforeProject { prj ->
    prj.apply from: 'http://www.tellurianring.com/projects/gradle-plugins/gradle-templates/apply.groovy'
  }
```

Type `gradle tasks` in any place where you want to create a gradle project, choose one from `Template Tasks`.

* Show dependencies `gradle dependencies`
* Specify source and test folders.
```groovy
sourceSets {
  main {
    java {
      srcDir 'mysubfolder/src/main/java'
    }
    resources {
      srcDir 'mysubfolder/src/main/resources'
    }
  }
  test {
    java {
      srcDir 'mysubfolder/src/test/java'
    }
    resources {
      srcDir 'mysubfolder/src/test/resources'
    }
  }
}
```
* Configue dependencies using local jar files
```groovy
dependencies {
  compile fileTree (dir: 'file:/home/edfeng/somedir', includes: ['*.jar'])
  testCompile fileTree (dir: 'file:../../somedir', includes: ['ViewController.jar'])
}
```

* Show all rumtime dependencies(This can also print out local libaries)
```groovy
task printAllDependencies() {
      configurations.runtime.each { File f -> println f }
}
```


