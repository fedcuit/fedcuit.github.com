---
layout: post
title: Git Visual Reference Notes 
date: 2012-08-11 00:59
comments: true
categories: git 
---

This notes is written after reading [A Visual Git Reference](http://marklodato.github.com/visual-git-guide/index-en.html)

![basic usage](http://marklodato.github.com/visual-git-guide/basic-usage.svg)

* **git add** *copy file from working directory to INDEX/stage*

* **git commit** *save a snapshot of files in INDEX/stage to repo*
   * `git commit -a`  
   (first git add all filenames that existed in the latest commit, and then running git commit) when we only modified some file after last commit, then we can directly use `git commit -a` to skip the git add step.

* **git checkout** *copy from commit history(local repo) to INDEX and working directory*
   * `git checkout HEAD files`   
   copies files from the latest commit to both the stage and the working directory.
   * `git checkout local_branch`  
   if files are not passed, then copy all files from that commit point to both INDEX and working directory, and remove files not in that commit.
   * `git checkout tag/remote_branch/commit-hash/relative-commit files`   
   copy files from specified commit to INDEX and working directory, if commit-hash is not passed, then copy files from INDEX to working directory.
   * `git checkout tag/remote_branch/commit-hash/relative-commit` 
    **IF**
     1. files are not passed 
     2. not checkout from a local branch    
   **THEN** an anonymous branch is created call detached HEAD, afterward commit will be base on detached HEAD, if switch to another branch, all commit in detached HEAD will be lost, if commits want be stored, it's better to use 'git checkout -b new_branch_name' to create a branch rather than use the anonymous branch.

* **git reset** *Move current branch to another position, and update INDEX*
   * `git reset commit-hash`
   move current branch(HEAD) to specified position, and update stage to match the commit-hash. if --hard is given, also update working directory. if --soft is given, neither is updated.
   * `git reset` 
   no commit-hash given, default to HEAD, move to HEAD, means, branch is not moved, but stage is updated with last commit.
   * `git reset [commit-hash] files` 
   move to specified commit-hash and update files in INDEX to match that commit. If commit-hash it not given, it defaults to HEAD.

* **git merge**
   * `git merge from_branch` 
   if other commit is ancestor of current commit, do nothing. if current commit is ancestor of other commit, results in a fast-forward merge. if current commit and other commit are not on the same line, then a real merge occur, git find out the common ancestor of the two commits, do merge with the three part, saving the result to both index and working directory, if no conflict, then do a new commit.

* **git cherry-pick**
   * `git cherry-pick commit-on-other-branch`
   copy a commit, creating a new commit on the current branch with the same message and patch as another commit.

* **git rebase** *replay commits of another branch onto the current branch, it just like a series of git cherry-pick call*
   * `git rebase from_branch`
   git merge will generate a new commit with two parents, but after git rebase, the last new commit only have one parent, and all old commits will be deleted.

