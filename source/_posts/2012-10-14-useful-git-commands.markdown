---
layout: post
title: 实用git命令
date: 2012-10-14 18:02
comments: true
categories: git
---

* `git reflog`  
    Reflog is a mechanism to record when the tip of branches are updated. This command is to manage the information recorded in it.  

    应用场景:

    1. 当前的working directory的git log如下图所示: 
        ![git status](/images/2012-10-14-useful-git-commands/before-reset.jpg)
    2. 用户切换到commit 041f088去查看几天之前系统的某个功能，执行:
        `git reset --hard 041f088`
    3. 当前的working directory的git log如下图所示: 
        ![git status](/images/2012-10-14-useful-git-commands/after-reset.jpg)
    4. 如果想切换回最新代码, 却发现已经不知道之前的HEAD的commit hash, 此时就可以使用`git reflog`命令来查看当前分支上的所有操作记录, 从中找到最新代码对应的commit hash:
        ![git status](/images/2012-10-14-useful-git-commands/reflog.jpg)
        从`git reflog`显示的操作记录来看，我们是从commit 4a56876 执行reset操作的，所以之前的HEAD是 4a56876, 执行`git reset --hard 4a56876`就可以切换回之前的代码.
        

    

