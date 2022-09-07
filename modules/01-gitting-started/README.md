# Module 1: Gitting Started

## Prerequisites

* Git is installed on your local computer
* You have an account registered with GitHub.com and are currently logged in.

## What is Git?

According to the official Git website,

> Git is a free and open source distributed version control system designed to handle everything
from small to very large projects with speed and efficiency.

It's important to understand that Git is a distributed version control system, not a client-server
system like SVN. Each copy of a Git repository is fully stand alone. Projects will often have a
main centralized repo hosted with an online provider such as [GitHub](https://github.com), but the
repo on GitHub and local clones of that repo are all standalone. A local repo does not "check out"
or "check in" files from the centralized server. Instead a local repo can have "remotes" configured
which are synchronized using push/pull/fetch mechanisms. Remotes will be covered in more detail
later in the workshop, but for now it's important to understand that there is no need for a central
server in Git.

The [official documentation](https://git-scm.com/doc) provides a set of
[Git training videos](https://git-scm.com/videos) as well as the
[Pro Git](https://git-scm.com/book/en/v2) book. You should start with these resources since they
will provide a conceptual overview of Git. By contrast the modules in this repo will focus on
practical hands-on examples.

## Create a new local repository

Start by creating a root repository directory to use for the workshop

```bash
# You can change this directory to wherever you'd like to store the files for this workshop
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

## Add a file

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

The new file `helloworld.txt` is identified by Git as "untracked" which means it is not currently under revision control. In Git a file can be in one of three areas:

1. The **working tree**: This is the folder on your computer where you edit files and this is
   where `helloworld.txt` is currently located.
2. The **staging area**: In Git changes must be staged before they are committed. The staging area
   is a temporary snapshot that can converted to a commit. After staging a file you can continue
   modifying the file in the working directory and then compare the file from the working directory
   against both the staging area and the repository. This can be helpful to save a known working
   copy of changes that you are still working on without creating a commit. A staged file that is
   modified in the working copy must be re-staged before it can be committed. Files in the staging
   area are sometimes referred to as "cached" and the staging area is also called the "index."
3. The **repository**: The repository is composed of a series of snapshots (commits) of the file
   contents over time. The other way of thinking of a commit is as a change set or patch.

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
# Skipping the -m flag will cause an editor to be opened instead for writing the commit message
~/src/git-workshop $ git commit -m "Initial commit"
[master (root-commit) 3e228e1] Initial commit
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 helloworld.txt

~/src/git-workshop $ git status
On branch master
nothing to commit, working tree clean
```

## Modify a file

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

Note that the file is now marked as modified instead of untracked.

Let's stage the changes to `helloworld.txt` and then modify it some more:

```bash
# Add all changes
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

# Diff the working tree against the staging area + repo
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
Note the difference between the working tree, the staging index, and the repo.

To accept the additional changes from the working tree we would use `git add` again. Instead let's
revert the working tree changes and only commit the changes that are already staged.

```bash
# Revert changes from the working tree
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

## Push the repo to GitHub

Go to https://github.com/new to create a new repository. Create the repository under your personal
account and name it `git-workshop`. Do not initialize the repository with a README, .gitignore, or
license.

After you create the GitHub repository you will be prompted with the steps required to add a remote to your local repo and then push the contents from your computer to GitHub. You can authenticate with GitHub using HTTPS or [SSH keys](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent).

```bash
# Add the GitHub repo as a remote named `origin`
# Uses SSH authentication
~/src/git-workshop $ git remote add origin git@github.com:<username>/git-workshop.git

# Rename the default branch from `master` to `main`
~/src/git-workshop $ git branch -M main

# Push the local main branch and setup an upstream (tracking) reference
~/src/git-workshop $ git push -u origin main
```

A local branch can have an upstream (tracking) reference. In this example, the local branch `main`
is now set to track the remote branch `origin/main`. This means that when `main` is checked out
locally `git push` will push commits from the local `main` branch to `origin/main` and `git pull`
will pull commits from `origin/main` to the local `main` branch. The upstream reference only needs
to be set when initially pushing a branch to a remote.

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
