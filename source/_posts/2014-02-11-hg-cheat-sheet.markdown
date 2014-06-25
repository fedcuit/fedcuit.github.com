---
layout: post
title: Hg cheat sheet
date: 2014-02-11 10:19
comments: true
categories: hg mercurial
---

* Remove unversioned file  
`hg purge`
* Discard local change  
`hg revert [FILE]`
* Rollback last commit  
`hg rollback`
* Looking for changes in the remote repository  
`hg incoming`
* Looking for changes you've made that aren't in the remote repository  
`hg outgoing`
* Reset to a given version (`reset --hard` in git)  
`hg strip -r commit_hash`


## _branch_
* Switch to another branch  
`hg update [BRANCH_NAME]`
* Show current branch name  
`hg branch`
* Merge changes from another branch to current branch  
`hg merge [ANOTHER_BRANCH_NAME]`
* Check logs on a given branch  
`hg log --only-branch my_branch`
