---
layout: post
title: Git - Tips and Notes
author: gini
categories: [ cloud ]
tags: [cloud, automation, containers, devops]
permalink: git
featured: false
hidden: false
image: "assets/images/tools-1426927308491-6380b6a9936f.jpeg"
#titleshort: Git
---

- [Git Cheat Sheet](#git-cheat-sheet)
- [How to clean Git commit history](#how-to-clean-git-commit-history)


## Git Cheat Sheet

```shell
# Checkout a new branch
git checkout -b new-branch

# push new branch to remote
git push -u origin new-branch
git push --set-upstream origin new-branch

# fetch the latest changes from remote
git fetch origin

# Merge the latest main into your branch
git merge origin/main
```

## How to clean Git commit history

```shell
# Create Orphan Branch
git checkout --orphan temp_branch

# Add Files to Branch and commit
git add -A
git commit -am "the first commit"

# Delete master Branch
git branch -D main

# Rename temp/Current Branch to master
git branch -m main

# Push Changes to repo as master
git push -f origin main
```

Original post: [How to Delete Commit History in Github](https://tecadmin.net/delete-commit-history-in-github/)