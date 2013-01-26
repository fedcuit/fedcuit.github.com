---
layout: post
title: 实用git命令
date: 2012-10-14 18:02
comments: true
categories: git
---
<!---
Remote 
-->

* `git remote add <remote-name> <remote-repo-url>`  
Add another remote for current git branch and name it with given name `remote-name`

* `git remote rename <original-name> <new-name>`  
Rename remote `original-name` to `new-name`

* `git config branch.<branch-name>.remote <remote-name>`  
Set the default remote of branch `branch-name` to `remote-name`, this command will generate below segment in `.git/config`:
``` bash
[branch "<branch-name>"]
    remote = <default-remote-name>
    merge = refs/heads/master
```
File `.git/config` is used to store branches and remotes information for a git repository.

* `git push <remote-name> [branch-name]`  
Push branch `branch-name` (If not specified, default is current branch) to remote `remote-name`.

***
<!---
Branch
-->
* `git branch -m <orginal-name> <new-name>`  
Rename branch `original-name` to given name `new-name`.

* `git checkout -b test origin/test`  
Git checkout remote branch

***
<!---
Misc
-->

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
* `git clean -f` `git clean -f -d`  
Remove untracked file from working directory. If `-d` used, will remove untracked directories in addition to untracked files. 
* `git add -p`  
Interactively choose hunks of patch between the index and the work tree and add them to the index. This gives the user a chance to review the difference before adding modified contents to the index.
