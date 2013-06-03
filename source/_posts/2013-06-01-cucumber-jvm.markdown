---
layout: post
title: Cucumber JVM
date: 2013-06-01 18:59
comments: true
categories: cucumber cucumber-jvm BDD java
---

##Cucumber-JVM command line interface
Usage: java cucumber.api.cli.Main [options] [ [FILE|DIR][:LINE[:LINE]*] ]+

Options:

* `-g`, --glue PATH                    Where glue code (step definitions and hooks) is loaded from.
* `-f`, --format FORMAT[:PATH_OR_URL]  How to format results. Goes to STDOUT unless PATH_OR_URL is specified.
   Available formats: junit, html, pretty, progress, json, json-pretty.
* `-t`, --tags TAG_EXPRESSION          Only run scenarios tagged with tags matching TAG_EXPRESSION.
* `-n,` --name REGEXP                  Only run scenarios whose names match REGEXP.
* `-d`, --[no-]-dry-run                Skip execution of glue code.
* `-m`, --[no-]-monochrome             Don't colour terminal output.
* `-s`, --[no-]-strict                 Treat undefined and pending steps as errors.
* `--dotcucumber` PATH_OR_URL      Where to write out runtime information. PATH_OR_URL can be a file system
    path or a URL.
* `-v`, --version                      Print version.
* `-h`, --help                         You're looking at it.

##IDE support
Be able to run *.feature files directly without any configuration in IntelliJ, you should put feature files as the same package structure as glue code.

Like:

--src
  --test
    --java
      --com
        --cukes
          --XXXStepdefs.java
          --YYYStepdefs.java
    --resources
      --com
        --cukes
          --features
            --XXX.feature
            --YYY.feature


