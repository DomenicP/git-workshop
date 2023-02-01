---
layout: post
title:  "Module 1: Gitting Started"
date:   2022-09-07 12:00:00 -0400
---

{% include modules.md %}

## Prerequisites

* Git is installed on your local computer
* You have an account registered with GitHub.com and are currently logged in.

## What is Git?

According to the official Git website,

> Git is a free and open source distributed version control system designed to handle everything from small to very large projects with speed and efficiency.

It's important to understand that Git is a distributed version control system, not a client-server system like SVN. Each copy of a Git repository is fully standalone. Projects will often have a main centralized repo hosted with an online provider such as [GitHub][github], but the repo on GitHub and local clones of that repo are all independent and self-sufficient. A local repo does not "check out" or "check in" files from the centralized server. Instead a local repo has all of the files and all of the content that it needs locally. Repos can be configured with [remotes][git-remotes] (independent repos on other machines/servers) that the local repo can synchronize with using [push][git-push] / [pull][git-pull] / [fetch][git-fetch] mechanisms. For now all that's important to understand is that there is no inherent need for a central server in Git.

The [official documentation][git-docs] provides a set of [Git training videos][git-vids] as well as the [Pro Git][pro-git] book. You should start with these resources since they will provide a conceptual overview of Git. By contrast the modules in this workshop will focus on practical hands-on examples.

[github]:      https://github.com
[git-remotes]: https://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes
[git-push]:    https://git-scm.com/docs/git-push
[git-pull]:    https://git-scm.com/docs/git-pull
[git-fetch]:   https://git-scm.com/docs/git-fetch
[git-docs]:    https://git-scm.com/doc
[git-vids]:    https://git-scm.com/videos
[pro-git]:     https://git-scm.com/book/en/v2

## Creating a new local repository

Start by creating a root repository directory to use for the workshop

```bash
# You can change this directory to wherever you'd like to store the files for this
# workshop
~ $ mkdir -p ~/src/git-workshop
~ $ cd ~/src/git-workshop

# Initialize a new local Git repository
~/src/git-workshop $ git init
Initialized empty Git repository in /home/<user>/src/git-workshop/.git/

# Check the status of the new repository
~/src/git-workshop $ git status
On branch master

No commits yet

nothing to commit (create/copy files and use "git add" to track)
```

## Adding a file

```bash
# Create an empty file in the new Git repo
~/src/git-workshop $ touch helloworld.txt

# Check the Git status again
~/src/git-workshop $ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        helloworld.txt

nothing added to commit but untracked files present (use "git add" to track)
```

The new file `helloworld.txt` is identified by Git as **untracked** which means it is not currently under revision control. In Git a file can be in one of three areas:

1. The **working tree**: This is the folder on your computer where you edit files and this is where `helloworld.txt` is currently located. Changes in the working tree can be compared against the repository using `git diff`.

2. The **staging area**: In Git changes must be staged using the `git add` command before they are committed. The staging area holds a temporary snapshot of changes that can either be committed or unstaged. A file that has staged changes can still be modified in the working tree. While iterating on code sometimes it's helpful to stage work-in-progress changes without creating a commit so that you can continue to experiment with additional changes but have the safety net of reverting to the staged changes.

   Staged changes can be compared against the repository using `git diff --cached`. You can also compare changes from the working tree against staged changes by using `git diff` after staging files with `git add` and making additional changes.

   You can create a commit from staged changes while still having unstaged modifications in the working tree. You can also use `git add` on a file that has already been staged to re-stage the all of the latest modifications from the working-tree. To discard staged changes without committing them you must first unstage them using `git restore` or `git reset`.

   The staging area is also referred to as the "index," and files that are staged are sometimes referred to as "cached."

3. The **repository**: The repository contains of a series of snapshots (commits) of the file contents over time. Each commit can also be thought of as a diff or changeset, and the Git history is built up by applying changesets sequentially over each previous revision.

Let's stage `helloworld.txt` and then create the first commit:

```bash
# Add a file
~/src/git-workshop $ git add helloworld.txt

~/src/git-workshop $ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   helloworld.txt

# Create a commit using the -m flag to specify a message
# Skipping the -m flag will cause a text editor to be opened
# where you can write the commit message.
~/src/git-workshop $ git commit -m "Initial commit"
[master (root-commit) 3e228e1] Initial commit
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 helloworld.txt

~/src/git-workshop $ git status
On branch master
nothing to commit, working tree clean
```

## Modifying a file

```bash
# Modify helloworld.txt
~/src/git-workshop $ echo "Hello, world!" > helloworld.txt

# Check Git status
~/src/git-workshop $ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   helloworld.txt

no changes added to commit (use "git add" and/or "git commit -a")

~/src/git-workshop $ git diff
git diff
diff --git a/helloworld.txt b/helloworld.txt
index e69de29..af5626b 100644
--- a/helloworld.txt
+++ b/helloworld.txt
@@ -0,0 +1 @@
+Hello, world!
```

Note that the file is now marked as **modified** instead of **untracked**.

Let's stage the changes to `helloworld.txt` and then modify it some more:

```bash
# Add all changes in the current directory
~/src/git-workshop $ git add .

~/src/git-workshop $ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   helloworld.txt

# Modify helloworld.txt again
~/src/git-workshop $ echo "another change" >> helloworld.txt

~/src/git-workshop $ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   helloworld.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   helloworld.txt

# Diff the working tree against the staging area
~/src/git-workshop $ git diff
diff --git a/helloworld.txt b/helloworld.txt
index af5626b..f64c220 100644
--- a/helloworld.txt
+++ b/helloworld.txt
@@ -1 +1,2 @@
 Hello, world!
+another change

# Diff the staging area against the repo
~/src/git-workshop $ git diff --cached
diff --git a/helloworld.txt b/helloworld.txt
index e69de29..af5626b 100644
--- a/helloworld.txt
+++ b/helloworld.txt
@@ -0,0 +1 @@
+Hello, world!
```
Note the difference between the file content in the working tree, the staging index, and the repo.

To stage the additional changes from the working tree we would use `git add` again. Instead let's discard the working tree changes using the command suggested by `git status` and only commit the changes that are already staged.

```bash
# Discard changes from the working tree
~/src/git-workshop $ git restore helloworld.txt

~/src/git-workshop $ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   helloworld.txt

# No diff between working tree and index
~/src/git-workshop $ git diff

# To view the staged changes use git diff --cached
~/src/git-workshop $ git diff --cached
diff --git a/helloworld.txt b/helloworld.txt
index e69de29..af5626b 100644
--- a/helloworld.txt
+++ b/helloworld.txt
@@ -0,0 +1 @@
+Hello, world!

# Commit the staged changes
~/src/git-workshop $ git commit -m "Add content to helloworld.txt"
[master 8adbc7b] Add content to helloworld.txt
 1 file changed, 1 insertion(+)
```

## Pushing the repo to GitHub

[Create a new repository using this link][new-repo]. Create the repository under your personal account and name it `git-workshop`. Do not initialize the repository with a `README`, `.gitignore`, or license file.

After you create the GitHub repository you will be shown the steps required to add a remote to your local repo and then push the contents from your computer to GitHub. You can authenticate GitHub using HTTPS or [SSH keys][ssh-keys]. In this workshop we'll use SSH authentication, and you can replace `<user>` with your GitHub username.

```bash
# Add the GitHub repo as a remote named `origin`
# Use SSH authentication
~/src/git-workshop $ git remote add origin git@github.com:<username>/git-workshop.git

# Rename the default branch from `master` to `main`
# Not required but suggested by GitHub. This discussion is beyond the scope of this
# workshop.
~/src/git-workshop $ git branch -M main

# Push the local main branch and setup an upstream (tracking) reference
~/src/git-workshop $ git push -u origin main
```

A local branch can have an upstream (tracking) reference. In this example, the local branch `main` is now set to track the remote branch `origin/main`. This means that when `main` is checked out locally `git push` will push commits from the local `main` branch to `origin/main` and `git pull` will pull commits from `origin/main` to the local `main` branch. The upstream reference only needs to be configured once; this typically happens when pushing a local branch to a remote or when checking out a remote branch locally.

[new-repo]: https://github.com/new
[ssh-keys]: https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

# Unstaging changes

```bash
# Add a new file
~/src/git-workshop $ echo "temp change" > temp.txt

~/src/git-workshop $ git status
On branch main
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        temp.txt

nothing added to commit but untracked files present (use "git add" to track)

# Stage the file
~/src/git-workshop $ git add temp.txt

~/src/git-workshop $ git status
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   temp.txt

# Unstage all staged files
~/src/git-workshop $ git restore --staged .

~/src/git-workshop $ git status
On branch main
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        temp.txt

nothing added to commit but untracked files present (use "git add" to track)

# Remove the file to get back to a clean working tree
~/src/git-workshop $ rm temp.txt

~/src/git-workshop $ git status
On branch main
nothing to commit, working tree clean
```

## Untracked files vs. directories

```bash
# What happens if we add an empty directory instead of a file?
~/src/git-workshop $ git status
On branch main
nothing to commit, working tree clean

~/src/git-workshop $ mkdir test

~/src/git-workshop $ git status
On branch main
nothing to commit, working tree clean
```

This example shows that Git only tracks files and will not track empty directories. If you would like to commit a directory that does not contain any files (maybe you intend to add files later but want to make the directory now) one option is to add an empty `.gitkeep` file to the directory. Technically any name will work for the blank file, but `.gitkeep` is a common standard for this purpose. The `.gitkeep` file can later be deleted when there are other files stored in the directory.

```bash
# Add a .gitkeep file to our new directory
~/src/git-workshop $ touch test/.gitkeep

~/src/git-workshop $ git status
On branch main
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        test/

nothing added to commit but untracked files present (use "git add" to track)

# Let's get rid of the directory rather than committing it
~/src/git-workshop $ rm -rf test

~/src/git-workshop $ git status
On branch main
nothing to commit, working tree clean
```

# Recap

In this module you learned:

* How to create a new local Git repository
* The difference between the working tree and the staging area
* How to add untracked files
* How to modify tracked files
* How to view diffs for both the working tree and staged files
* How to create a new GitHub repository
* How to add a remote to a local repo
* How to push a branch and create an upstream (tracking) reference
* How to unstage changes
* How to convince Git to track an "empty" folder
