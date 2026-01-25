---
layout: post
title: "Git Stash: Save Your Work Without Messy Commit"
date: 2026-01-24 09:00:00 +0000
categories: [Programming, Git]
tags: [Git, Workflow, Productivity]
---



While programming, there are many situations where I want to temporarily save my current changes and switch to another branch. For example, I might be in the middle of my own task when someone asks me to fetch their repository from a remote server and review their code.  

When I was a junior developer, I used to commit my work as `WIP` and push it to the repository. This worked, but it made the commit history messy and hard to follow.

Later, I discovered the **`git stash`** command, which turned out to be a much cleaner and safer way to handle these situations.

This post is a short note for future reference. A deeper explanation of how Git works internally will be documented separately.

---

## 1. Stash tracked and untracked files

```bash
git stash push -u

# example
git stash push -u -m "Save all changes including untracked"
```
-u is short for --include-untracked.


| Files included                | Command        |
| ----------------------------- | -------------- |
| Tracked only                  | `git stash`    |
| Tracked + untracked           | `git stash -u` |
| Tracked + untracked + ignored | `git stash -a` |


## 2. List your stashes

```bash
git stash list

# example output
stash@{0}: WIP on main: abc1234 Add new feature
stash@{1}: WIP on main: def5678 Fix bug
```
This shows all saved stashes in chronological order, with the most recent one at the top.

## 3. Apply only specific files from a stash
If you only want to restore specific files from a stash, use git checkout with the stash reference:
```bash
git checkout stash@{0} --path/to/file1 path/to/file2
```
This is useful when you don’t want to apply all changes from the stash.


## 4. Apply all changes from a specific stash
```bash
git stash apply stash@{0}
```
This applies the stash without removing it from the stash list.

If you want to apply the stash and remove it at the same time:
```bash
git stash pop stash@{0}
```
This applies the stash and removes it from the stash list.



## 5. Best Practices for Using Git Stash
- Use Descriptive Messages: Helps in identifying different stashes easily.
- Regularly Clean Up Stashes: Apply or drop stashes to avoid unnecessary clutter.
- Be Mindful of Conflicts: Applying a stash may lead to conflicts that need manual resolution.
- Stash Untracked Files When Necessary: Use -u if we need to save untracked files.
- Check our Work Before Stashing: Ensure we don’t stash incomplete or unnecessary changes.

**Reference:**  
[GeeksforGeeks – Git Stash Best Practices](https://www.geeksforgeeks.org/git-stash/)
