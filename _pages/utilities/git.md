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
- [How to fetch latest content from main repo to forked repo](#how-to-fetch-latest-content-from-main-repo-to-forked-repo)
- [Create a PR to main](#create-a-pr-to-main)


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

## How to fetch latest content from main repo to forked repo

Follow these steps to update your forked repository with the latest changes from the original (eg. `main` or `master`) repository.

**1. Add the original repo as `upstream` (only once)**

```bash
git remote add upstream <original repo URL>
```

> Skip this step if you've already added `upstream`. You can verify with:

```bash
git remote -v
```

**2. Fetch the latest changes from the original repo**

```bash
git fetch upstream
```

**3. Merge or rebase changes into your local branch**

Make sure you’re on your working branch (e.g. `master` or `main`):

```bash
git checkout master
git merge upstream/master
```

> Optional: Use rebase for cleaner history:

```bash
git checkout main
git rebase upstream/main
```

**4. Push the updated branch to your fork**

```bash
git push origin main
```

Now your forked repository is up to date with the latest changes from the main/original repository.

## Create a PR to main

- [Install `gh` utility](https://github.com/cli/cli/blob/trunk/docs/install_linux.md)
- Authenticate with `gh login`

1. Raise a PR

```shell
gh pr create \
  --base main \
  --head cf-pages \
  --title "Deploy blog updates" \
  --body "Added new blog post and updates to site"
```

2. Approve the PR (from CLI)

```shell
gh pr review <PR_NUMBER_OR_URL> --approve
```

3. Merge the PR

```shell
gh pr merge <PR_NUMBER_OR_URL> --merge
```