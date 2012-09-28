---
layout: post
title: Mac配置Octopress问题汇集
date: 2012-08-02 20:59
comments: true
categories: mac octopress github 
---
##Psych::SyntaxError
在mac上安装完octopress后执行`rake new_post[\title\]`提示下面的错误信息:  
``` bash
    ~/.rvm/rubies/ruby-1.9.2-p320/lib/ruby/1.9.1/psych.rb:148:in `parse': couldn't parse YAML at line 4 column 0 (Psych::SyntaxError)`
```

解决办法:  
打开文件`~/.rvm/gems/ruby-1.9.2-p320/gems/jekyll-0.11.2/lib/jekyll/convertible.rb`, 将下面的代码添加到文件首部:  

``` ruby
require 'yaml'
YAML::ENGINE.yamler = 'syck'
```


##YAML Exception  
执行`rake generate`后提示下面的错误消息:  
``` bash
    ## Generating Site with Jekyll
    unchanged sass/screen.scss
    Configuration from /Users/edfeng/workspace/octopress/_config.yml
    Building site: source -> public
    YAML Exception reading 2012-08-02-config-octopress-on-mac.markdown: syntax error on line 7, col -1: `'
    Successfully generated site: source -> public'`
```
解决办法:  
出现上面的错误是因为**markdown**文件的文件头没有遵循*yaml*规范: *yaml文件中冒号后面必须有空格*
