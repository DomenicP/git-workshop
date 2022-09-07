# Module 2: Feature Branch Workflow

## Prerequisites

* Successfully completed Module 1

## Visualizing Git history

Git history is a graph where each node is a commit that points back to a parent (or two parents in
the case of a merge commit). It's important to have good tools to visualize Git history in order to
understand what's going on. Various Git GUIs are available as well as plugins for editors (such as
[Git Graph](https://marketplace.visualstudio.com/items?itemName=mhutchie.git-graph)). You can also
use `git log` from the command line to generate visualizations of Git history

```bash
# Regular `git log`
~/src/git-workshop $ git log
commit 8adbc7bbbe8495d134d9cbd72749b94c314ce539 (HEAD -> main, origin/main)
Author: ...
Date:   Wed Sep 7 14:31:10 2022 -0400

    Add content to helloworld.txt

commit 3e228e13d776d3a797a723cf7bd95b1025d450be
Author: ...
Date:   Wed Sep 7 14:18:20 2022 -0400

    Initial commit

# `git log` with a nice tree view
# Output in your terminal should have pretty colors
# Consider creating a `gl` alias so you don't have to remember all of these flags
~/src/git-workshop $ git log --oneline --graph --decorate --all
* 8adbc7b (HEAD -> main, origin/main) Add content to helloworld.txt
* 3e228e1 Initial commit
```

Branches in Git are lightweight and can easily be created and destroyed as needed. A branch is just
a pointer to a commit. Because Git is a distributed version control system the local repo and remote
repos each have their own individual branches. Remote branches are prefixed by their remote name
(such as `origin/main` in the example above).

## Detached HEAD

In Git the special term `HEAD` is a pointer to the current branch (shown as `HEAD -> main` in the
log output above). Git allows you to check out branches but also to check out individual commits.
Checking out individual commits puts you into a **detached head** state; this is something that is
pretty easy to accidentally do when you're still new to Git. Let's intentionally enter a
detached head by checking out the initial commit (note that you'll need to update commit hashes for
your local repo since they will be different than the values on this page):

```bash
# Checkout a specific commit by hash
~/src/git-workshop $ git checkout 3e228e1
Note: switching to '3e228e1'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:

  git switch -c <new-branch-name>

Or undo this operation with:

  git switch -

Turn off this advice by setting config variable advice.detachedHead to false

HEAD is now at 3e228e1 Initial commit

# TL;DR: we're now not on any branch. Let's see what that looks like in terms of the graph
~/src/git-workshop $ gl
* 8adbc7b (origin/main, main) Add content to helloworld.txt
* 3e228e1 (HEAD) Initial commit
```

(The Git output above mentions the new `git switch` command that was recently added. You are welcome
to investigate using this command but this workshop will use the more traditional `git checkout`
command instead.)

After checking out a specific commit by hash, `HEAD` now points to a commit instead of a branch.
From this state you can either create a new branch at the commit that is checked out or you can
checkout a branch again. Let's go back to `main`

```bash
~/src/git-workshop $ git checkout main
Previous HEAD position was 3e228e1 Initial commit
Switched to branch 'main'
Your branch is up to date with 'origin/main'.

~/src/git-workshop $ gl
* 8adbc7b (HEAD -> main, origin/main) Add content to helloworld.txt
* 3e228e1 Initial commit
```

## Create a feature branch

Let's create a new branch to do some work on:

```bash
~/src/git-workshop $ git checkout -b my-first-feature
Switched to a new branch 'my-first-feature'

~/src/git-workshop $ gl
* 8adbc7b (HEAD -> my-first-feature, origin/main, main) Add content to helloworld.txt
* 3e228e1 Initial commit
```

`git checkout -b <name>` does two things:

1. Creates a new branch called `<name>`.
2. Checks out the newly created branch.

From the `gl` output we see that `HEAD` now points to `my-first-feature` indicating that this is the
branch that is checked out and that there are three branches point to the same commit:

* `my-first-feature`
* `main`
* `origin/main`
  * Remember this is *not* the same branch as `main`, but instead it is GitHub's independent copy
    of the branch

We can use `git branch` to list all local branches, and `git branch -a` to list all known branches:

```bash
~/src/git-workshop $ git branch
  main
* my-first-feature

~/src/git-workshop $ git branch -a
  main
* my-first-feature
  remotes/origin/main
```

In the output above the `*` indicates the currently checked out branch. Your terminal may also
display remote branches (prefixed with `remotes/origin/`) in a different color.

## Push the feature branch to GitHub

Even though we haven't actually made any changes yet, we can still push the feature branch to
GitHub:

```bash
~/src/git-workshop $ git push -u origin my-first-feature
Total 0 (delta 0), reused 0 (delta 0)
remote:
remote: Create a pull request for 'my-first-feature' on GitHub by visiting:
remote:      https://github.com/<user>/git-workshop/pull/new/my-first-feature
remote:
To github.com:<user>/git-workshop.git
 * [new branch]      my-first-feature -> my-first-feature
Branch 'my-first-feature' set up to track remote branch 'my-first-feature' from 'origin'.

~/src/git-workshop $ gl
* 8adbc7b (HEAD -> my-first-feature, origin/my-first-feature, origin/main, main) Add content to helloworld.txt
* 3e228e1 Initial commit
```

Note that after pushing the branch to GitHub there is now an additional `origin/my-first-feature`
branch shown in the tree.

Let's actually do some work and commit the changes:

```bash
~/src/git-workshop $ echo "Awesome new feature" > first-feature.txt

~/src/git-workshop $ git add first-feature.txt

~/src/git-workshop $ git commit -m "Add a really cool feature"
[my-first-feature 70e6b9f] Add a really cool feature
 1 file changed, 1 insertion(+)
 create mode 100644 first-feature.txt

~/src/git-workshop $ git status
On branch my-first-feature
Your branch is ahead of 'origin/my-first-feature' by 1 commit.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean

~/src/git-workshop $ gl
* 70e6b9f (HEAD -> my-first-feature) Add a really cool feature
* 8adbc7b (origin/my-first-feature, origin/main, main) Add content to helloworld.txt
* 3e228e1 Initial commit
```

After committing `git status` tells us that `my-first-feature` is ahead of `origin/my-first-feature`
by 1 commit. This information is available because we created the upstream tracking reference when
initially pushing this branch to GitHub. `gl` confirms the same thing as `git status` showing that
the local branch points to the new commit and the remote branch still points where it started.

Let's follow Git's advice and push the new commit to GitHub:

```bash
~/src/git-workshop $ git push
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 4 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 332 bytes | 332.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To github.com:<user>/git-workshop.git
   8adbc7b..70e6b9f  my-first-feature -> my-first-feature

~/src/git-workshop $ gl
* 70e6b9f (HEAD -> my-first-feature, origin/my-first-feature) Add a really cool feature
* 8adbc7b (origin/main, main) Add content to helloworld.txt
* 3e228e1 Initial commit
```

Note that we could use `git push` by itself this time; we didn't need to specify the remote branch
because Git already knows from the tracking reference.

After pushing we see `my-first-feature` and `origin/my-first-feature` once again point to the same
commit. Manual synchronization between distributed copies of the repository is one of the most
important concepts to understand in Git, especially when coming from client-server systems like
SVN. Modifying a local branch (creating commits, resetting the branch, etc.) makes no changes to
remote branches until they are manually synchronized using `git push/pull/fetch`!

## Pull the feature branch on another machine

To make things more interesting, let's introduce another copy of the repository into the mix. Let's
say you've been working on your desktop up until now but now you'd like to transfer code onto your
laptop so that you can test it with some hardware. To simulate working on another computer, let's
clone a second copy of the repository on your local machine:

```bash
~/src/git-workshop $ cd ~/src

# Normally `git clone` would create a new folder in the current directory matching the repo name
# In this case we pass a second parameter to specify the name of the folder we want the repo
# to be stored in
~/src $ git clone git@github.com:<user>/git-workshop.git git-workshop-2
Cloning into 'git-workshop-2'...
remote: Enumerating objects: 9, done.
remote: Counting objects: 100% (9/9), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 9 (delta 1), reused 9 (delta 1), pack-reused 0
Receiving objects: 100% (9/9), done.
Resolving deltas: 100% (1/1), done.

~/src $ cd git-workshop-2

# Pay close attention to the current working directory from this point on
# We'll refer to this copy of the repo as the "laptop" and the original copy of the repo
# as the "desktop"
~/src/git-workshop-2 $ git status
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean

~/src/git-workshop-2 $ gl
* 70e6b9f (origin/my-first-feature) Add a really cool feature
* 8adbc7b (HEAD -> main, origin/main, origin/HEAD) Add content to helloworld.txt
* 3e228e1 Initial commit
```

Even though this secondary copy is still on the same computer we can pretend like it's on a
different machine; the behavior will be exactly the same.

The fresh clone of the repo in `git-workshop-2` has `main` checked out by default. Once again, note
that this `main` branch is a fully independent `main` branch from `origin/main` and `main` in the
original repo from `git-workshop`. However, `origin/main` in both the "desktop" and "laptop" copies
of the repo refers to the same branch on GitHub since `origin` points to the same remote repo in
both local repos.

Let's checkout the feature branch on the "laptop"

```bash
# "Laptop" repo
~/src/git-workshop-2 $ git checkout my-first-feature
Branch 'my-first-feature' set up to track remote branch 'my-first-feature' from 'origin'.
Switched to a new branch 'my-first-feature'

~/src/git-workshop-2 $ gl
* 70e6b9f (HEAD -> my-first-feature, origin/my-first-feature) Add a really cool feature
* 8adbc7b (origin/main, origin/HEAD, main) Add content to helloworld.txt
* 3e228e1 Initial commit
```

Let's take a look at what happened with this `git checkout` command. We didn't use the `-b` flag,
however a new local branch was still created. Why? Because Git recognized that there was a remote
branch named `my-first-feature` and took initiative to do the following:
* Git created a new local branch named `my-first-feature` that points to the same commit as
  `origin/my-first-feature`
* Git setup upstream tracking between the new local `my-first-feature` branch and
  `origin/my-first-feature`
* Git checked out the new local `my-first-feature` branch.

**IMPORTANT**: This only worked because we asked git to checkout `my-first-feature`, not
`origin/my-first-feature`. If we had instead run the command `git checkout origin/my-first-feature`
we would have ended up in a detached HEAD state!

```bash
# Just for illustration, you don't need to run these commands
~/src/git-workshop-2 $ git checkout origin/my-first-feature
Note: switching to 'origin/my-first-feature'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:

  git switch -c <new-branch-name>

Or undo this operation with:

  git switch -

Turn off this advice by setting config variable advice.detachedHead to false

HEAD is now at 70e6b9f Add a really cool feature

# Yikes, big oof, let's get back to the local branch
~/src/git-workshop-2 $ git checkout my-first-feature
Switched to branch 'my-first-feature'
Your branch is up to date with 'origin/my-first-feature'.
```

## Make commits from the other machine

Now that we're working on the "laptop" let's go ahead and make some changes:

```bash
# From the laptop repo
~/src/git-workshop-2 $ echo foo >> first-feature.txt

# Use the `git commit -a` flag to automatically stage and commit in one action
# This doesn't work for untracked files
~/src/git-workshop-2 $ git commit -am "Integration testing"
[my-first-feature c9698e5] Integration testing
 1 file changed, 1 insertion(+)

# Make some more changes
~/src/git-workshop-2 $ echo bar >> first-feature.txt

# These are lazy commit messages, write better messages IRL
~/src/git-workshop-2 $ git commit -am "More integration testing"
[my-first-feature 61284ab] More integration testing
 1 file changed, 1 insertion(+)

~/src/git-workshop-2 $ echo baz >> first-feature.txt

~/src/git-workshop-2 $ git commit -am "Finally got it working"
[my-first-feature 5ac8ea0] Finally got it working
 1 file changed, 1 insertion(+)

# Let's see the results of our work
~/src/git-workshop-2 $ git status
On branch my-first-feature
Your branch is ahead of 'origin/my-first-feature' by 3 commits.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean

~/src/git-workshop-2 $ gl
* 5ac8ea0 (HEAD -> my-first-feature) Finally got it working
* 61284ab More integration testing
* c9698e5 Integration testing
* 70e6b9f (origin/my-first-feature) Add a really cool feature
* 8adbc7b (origin/main, origin/HEAD, main) Add content to helloworld.txt
* 3e228e1 Initial commit
```

Just like before, the local `my-first-feature` branch moved forward with the new commits but the
remote branch on GitHub is out of date. Let's push the local commits to GitHub:

```bash
# Still on the "laptop"
~/src/git-workshop-2 $ git push
Enumerating objects: 11, done.
Counting objects: 100% (11/11), done.
Delta compression using up to 4 threads
Compressing objects: 100% (6/6), done.
Writing objects: 100% (9/9), 946 bytes | 946.00 KiB/s, done.
Total 9 (delta 0), reused 0 (delta 0)
To github.com:<user>/git-workshop.git
   70e6b9f..5ac8ea0  my-first-feature -> my-first-feature
```

With everything tested and working, let's switch back to the desktop and clean things up before
opening a PR:

```bash
# Switch back to the "desktop"
~/src/git-workshop-2 $ cd ~/src/git-workshop

# Check on the status
~/src/git-workshop $ git status
On branch my-first-feature
Your branch is up to date with 'origin/my-first-feature'.

nothing to commit, working tree clean

~/src/git-workshop $ gl
* 70e6b9f (HEAD -> my-first-feature, origin/my-first-feature) Add a really cool feature
* 8adbc7b (origin/main, main) Add content to helloworld.txt
* 3e228e1 Initial commit
```

Huh, that's funny. Where are the new commits and why does the "desktop" Git repo think everything is
up to date? Until we run a command that synchronizes out "desktop" Git repo with GitHub we won't
have information about the work done elsewhere. There are two primary ways for Git to retrieve
information from a remote: `git pull` and `git fetch`.

`git fetch` retrieves branches, tags, and objects from a remote repository to update the local
history. For a local repo with one remote (typically `origin`) `git fetch` will fetch from the one
and only remote. If your local repo is configured with more than one remote you may need to specify
which remote you want to fetch from or use the `--all` flag. Using the `-p` and `-t` flags can be
helpful since they do the following:

* `-p, --prune`: Remove local references to remote branches that were deleted (this is *not* done
  by default)
* `-t, --tags`: Fetch all tags from the remote (more on tags later)

`git pull` is shorthand for `git fetch` followed by `git merge`. `git pull` is convenient for
quickly pulling in updates from a remote branch but can be dangerous if history has diverged between
the local repo and remote repo (more on this later). `git pull` risks creating a merge commit or
starting a rebase. To avoid this risk use `git pull --ff-only` which will only work if the local
branch can be **fast-forwarded** (i.e. the local branch pointer can be advanced to catch up to the
remote branch pointer without creating any new commits or needing any merge/rebase). If
`git pull --ff-only` fails then you can use `git fetch` along with the methods described later in
this module to resolve the situation without accidentally creating a merge commit.

Let's do a `git fetch` now followed by a `git merge --ff-only` to update our "desktop" branch to
match GitHub:

```bash
# Fetch from origin (GitHub)
~/src/git-workshop $ git fetch -pt
remote: Enumerating objects: 11, done.
remote: Counting objects: 100% (11/11), done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 9 (delta 0), reused 9 (delta 0), pack-reused 0
Unpacking objects: 100% (9/9), 926 bytes | 926.00 KiB/s, done.
From github.com:<user>/git-workshop
   70e6b9f..5ac8ea0  my-first-feature -> origin/my-first-feature

# See the new commits
~/src/git-workshop $ gl
* 5ac8ea0 (origin/my-first-feature) Finally got it working
* 61284ab More integration testing
* c9698e5 Integration testing
* 70e6b9f (HEAD -> my-first-feature) Add a really cool feature
* 8adbc7b (origin/main, main) Add content to helloworld.txt
* 3e228e1 Initial commit

# Advance the local branch to match the remote
# Could also use `git pull` or `git pull --ff-only` in this case
~/src/git-workshop $ git merge --ff-only
Updating 70e6b9f..5ac8ea0
Fast-forward
 first-feature.txt | 3 +++
 1 file changed, 3 insertions(+)

# Check that everything worked correctly
~/src/git-workshop $ gl
* 5ac8ea0 (HEAD -> my-first-feature, origin/my-first-feature) Finally got it working
* 61284ab More integration testing
* c9698e5 Integration testing
* 70e6b9f Add a really cool feature
* 8adbc7b (origin/main, main) Add content to helloworld.txt
* 3e228e1 Initial commit
```

## Squash intermediate commits

When we were doing integration testing on the laptop we made a bit of a mess with those intermediate
work-in-progress commits. Let's clean things up and squash those three commits down to a single
commit.

**WARNING**: This is where we have to start being a little more careful since this is the first
time that we're rewriting Git history. Some developers are opposed to this practice, but there can
be value to editing the revision history to be more meaningful and get rid of noise instead of
insisting that every commit ever created be kept forever.

The standard way to squash commits is by doing an interactive rebase. This is an advanced topic
and the interactive rebase tool can be used to do a lot of things. For now we'll only worry about
squashing commits.

First let's answer the question "what is the base of a branch?" Every branch in Git has a starting
point; this is the first commit where the branch splits off. Look back to the most recent `gl`
output. In this case we would say that the commit with hash `5ac8ea0` is the tip of the
`my-first-feature` branch. The branch reference itself points to the tip of the branch, and the base
of the `my-first-feature` branch is `8adbc7b` (i.e. `main`). `70e6b9f` is the first commit
specifically on the `my-first-feature` branch, so it's parent is the base of the branch.

Note that even if the `main` branch moved forward to point to a new commit (such as through a pull
request merging to `main`) `8adbc7b` would remain the base of the `my-first-feature` branch. All
that would change is that we could no longer refer to the base of the branch as `main` since that
pointer moved. "Rebasing" refers to re-applying commits on top of a new base commit (or in some
cases the same base) and the commits can be edited along the way.

Enough theory for now, let's get to squashing commits with an interactive rebase:

```bash
# my-first-feature branch is implied since it is checked out
# main branch is currently the base and we don't want to change that so we rebase on top of the
# same base as before
~/src/git-workshop $ git rebase -i main
# The following should open inside of your default CLI text editor
pick 70e6b9f Add a really cool feature
pick c9698e5 Integration testing
pick 61284ab More integration testing
pick 5ac8ea0 Finally got it working

# Rebase 8adbc7b..5ac8ea0 onto 5ac8ea0 (4 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message.
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```

So what just happened? When starting an interactive rebase Git opens a file in your text editor
called `git-rebase-todo`. This file controls the rebase process and we'll edit it to tell Git how
we want to squash the commits. At the top of the file we see the commits from `my-first-feature`
with the oldest commit at the top and the newest commit at the bottom. Git will go through this
list from top to bottom re-applying the commits on top of the base commit that we specified (which
in this case was still `main`). Different commands are available when applying a commit; in this
case we are interested in the `p` (pick) and `s` (squash) commands. `p` is the default and results
in no changes to the commit. By specifying `s` to squash a commit we can combine multiple commits
into one. Commits are squashed up into the previous commit, so somewhere along the chain you'll
need to `pick` at least one commit. Let's edit the rebase todo to match the following

```bash
pick 70e6b9f Add a really cool feature
pick c9698e5 Integration testing
s 61284ab More integration testing
s 5ac8ea0 Finally got it working
# Rest omitted for sake of space; comment lines are ignored anyway
```

This tells Git that we want to squash `5ac8ea0` up into `61284ab` and then up into `c9698e5` as the
squashed commit. We'd like to keep `70e6b9f` as its own commit without changing it. After saving and
quitting the editor it's time to Git to work.

Git opens another file in the editor that should look something like the following:

```bash
# This is a combination of 3 commits.
# This is the 1st commit message:

Integration testing

# This is the commit message #2:

More integration testing

# This is the commit message #3:

Finally got it working

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# Date:      Wed Sep 7 15:51:35 2022 -0400
#
# interactive rebase in progress; onto 8adbc7b
# Last commands done (4 commands done):
#    s 61284ab More integration testing
#    s 5ac8ea0 Finally got it working
# No commands remaining.
# You are currently rebasing branch 'my-first-feature' on '8adbc7b'.
#
# Changes to be committed:
#       modified:   first-feature.txt
#
```

This file allows us to edit the commit message for the combined squashed commit. In this case we
don't need to change anything, so we save and exit the editor to continue:

```bash
...
[detached HEAD 99f448c] Integration testing
 Date: Wed Sep 7 15:51:35 2022 -0400
 1 file changed, 3 insertions(+)
Successfully rebased and updated refs/heads/my-first-feature.

# Let's see what happened...
~/src/git-workshop $ git status
On branch my-first-feature
Your branch and 'origin/my-first-feature' have diverged,
and have 1 and 3 different commits each, respectively.
  (use "git pull" to merge the remote branch into yours)

nothing to commit, working tree clean

# ...and take a look at the graph (which isn't a straight line anymore!)
~/src/git-workshop $ gl
* 99f448c (HEAD -> my-first-feature) Integration testing
| * 5ac8ea0 (origin/my-first-feature) Finally got it working
| * 61284ab More integration testing
| * c9698e5 Integration testing
|/
* 70e6b9f Add a really cool feature
* 8adbc7b (origin/main, main) Add content to helloworld.txt
* 3e228e1 Initial commit
```

With the rebase complete our local version of the Git history on the "desktop" is now different
than what GitHub thinks the Git history should be. Both repos agree that `70e6b9f` is the first
commit on the `my-first-feature` branch, but the local branch only has one `99f448c` commit that
comes after while the remote branch still has the three original commits. Just to be sure, let's
diff between the local and remote branch to make sure the actual content is the same

```bash
# This isn't normally necessary but just for sake of illustation in this example
~/src/git-workshop $ git diff origin/my-first-feature

# No output because the branch content is identical
```

Since we intentionally squashed the commits down in the local repo we know that the local repo is
the current source of truth for the Git history. We would like to force other copies of the repo
to update to match this new version of history. To tell GitHub that we want to overwrite the remote
history to forcibly match the local history instead, all we need to do is `git push --force`.

```bash
# Time to force push
~/src/git-workshop $ git push --force
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 4 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 367 bytes | 367.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To github.com:<user>/git-workshop.git
 + 5ac8ea0...99f448c my-first-feature -> my-first-feature (forced update)

# Check the updated graph
~/src/git-workshop $ gl
* 99f448c (HEAD -> my-first-feature, origin/my-first-feature) Integration testing
* 70e6b9f Add a really cool feature
* 8adbc7b (origin/main, main) Add content to helloworld.txt
* 3e228e1 Initial commit
```

After the force push the remote repo replaces its history to match what we forcibly pushed. We are
now in agreement again between the "desktop" repo and GitHub.

**WARNING**: Again, this is dangerous territory; please force push responsibly. Rewriting history
and force pushing should only be done on branches for which you are the sole author or after
carefully coordinating with teammates on a shared branch. Before force pushing you need to know
which repo has the actual source of truth and that meaningful changes haven't been introduced in
another copy of the repo. After force pushing when switching to another machine or repo copy make
sure that local repo has been updated to match the new overwritten history from the remote.

## Update the other machine after rewriting history

We left off with the "desktop" and GitHub repos agreeing to the newly squashed commits, but the
"laptop" is still out of the loop. Let's bring it up to speed

```bash
# Switch back to the "laptop"
~/src/git-workshop $ cd ~/src/git-workshop-2

# Probably not safe to do a `git pull` here, let's fetch instead
~/src/git-workshop-2 $ git fetch -pt
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 347 bytes | 347.00 KiB/s, done.
From github.com:<user>/git-workshop
 + 5ac8ea0...99f448c my-first-feature -> origin/my-first-feature  (forced update)

~/src/git-workshop-2 $ git status
On branch my-first-feature
Your branch and 'origin/my-first-feature' have diverged,
and have 3 and 1 different commits each, respectively.
  (use "git pull" to merge the remote branch into yours)

nothing to commit, working tree clean

~/src/git-workshop-2 $ gl
* 99f448c (origin/my-first-feature) Integration testing
| * 5ac8ea0 (HEAD -> my-first-feature) Finally got it working
| * 61284ab More integration testing
| * c9698e5 Integration testing
|/
* 70e6b9f Add a really cool feature
* 8adbc7b (origin/main, origin/HEAD, main) Add content to helloworld.txt
* 3e228e1 Initial commit
```

The "laptop" repo and GitHub disagree on history. If we `git pull` or `git merge` in this case we
risk introducing an unwanted merge commit. If we didn't `git fetch` first and see that the history
diverged then we may have worked directly on the old copy of the branch which creates a mess that
has to be cleaned up later. Always stay aware of when the branch history is edited; this is another
reason to limit the scope of branch history edits to personal branches for which you know the full
history. Long-running branches (`main`, `develop`) should never have their history edited unless
it is absolutely necessary, the need has been clearly communicated to all team members, and the edit
is done by a project admin.

How do we resolve the diverged history between the "laptop" and GitHub? We have a few options
available

* Delete the local branch and checkout a fresh copy from GitHub
  * For example (don't run this)
  * `git checkout main && git branch -d my-first-feature && git checkout my-first-feature`
* Do a hard reset on the local branch to force it to point to the correct commit

For either case, make sure there are no local changes that only exist on this old copy of the branch
before fixing the branch otherwise those changes will be lost!

Let's do a hard reset in this case:

```bash
~/src/git-workshop-2 $ git reset --hard origin/my-first-feature
HEAD is now at 99f448c Integration testing

~/src/git-workshop-2 $ gl
* 99f448c (HEAD -> my-first-feature, origin/my-first-feature) Integration testing
* 70e6b9f Add a really cool feature
* 8adbc7b (origin/main, origin/HEAD, main) Add content to helloworld.txt
* 3e228e1 Initial commit
```

Now everyone agrees on the history of the branch and it is safe to continue working from this
machine.

**NOTE**: Even without squashing commits it is possible for history to diverge between copies of a
repository when a branch is being modified from multiple copies of the repo at the same time
without synchronizing. To avoid this headache, only work on the branch from one copy of the repo at
a time and be sure to synchronize with the latest copy before starting any local work on a branch.

## Squash and merge a PR

After much hard work our feature is finally finished! Let's open a pull request (PR) on GitHub and
merge the branch back to `main`.

Browse to the repository on GitHub. The main repo page may have a "Compare & pull request" button
automatically if you pushed the branch to GitHub recently. If not, click on "2 branches" next to the
"main" branch dropdown to browse to `https://github.com/<user>/git-workshop/branches`. At the far
right side of your branch entry click the option to create a new pull request. Pull request details
are outside the scope of this module; for now add a nice title and description and then click the
green "Create pull request" button.

The PR page on GitHub allows you to collaboratively review your code changes before merging. In this
case let's go ahead and merge right away. Scroll down towards the button at the bottom of the page
labeled "Merge pull request." Click on the dropdown arrow on the right side of this button and
instead select "Squash and merge." This option will squash all of the commits from this PR down to
a single commit and then rebase the squashed commit to the tip of `main` (which is the PR target
in this case). After confirming the squash and merge, you can click the button that appears to
delete the remote branch from GitHub. With the PR merged we no longer need the branch.

Let's go back to the "desktop" repo and fetch the latest changes after merging the PR

```bash
# Switch back to the "desktop" repo
~/src/git-workshop-2 $ cd ~/src/git-workshop

# Fetch changes from the remote
# Note that without the -p flag this wouldn't delete the reference to origin/my-first-feature
~/src/git-workshop $ git fetch -pt
From github.com:<user>/git-workshop
 - [deleted]         (none)     -> origin/my-first-feature
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 2 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 763 bytes | 763.00 KiB/s, done.
   8adbc7b..5a91f76  main       -> origin/main

~/src/git-workshop $ gl
* 5a91f76 (origin/main) My first feature (#1)
| * 99f448c (HEAD -> my-first-feature) Integration testing
| * 70e6b9f Add a really cool feature
|/
* 8adbc7b (main) Add content to helloworld.txt
* 3e228e1 Initial commit
```

The `gl` output shows us that a new squashed commit was created at `origin/main`. Our local copy of
`main` is still where it was and our local copy of the `my-first-feature` branch still exists. Let's
diff between `my-first-feature` and `origin/main` to confirm that everything is the same

```bash
~/src/git-workshop $ git diff origin/main

# No output, everything is the same
```

To wrap things up in our local repo we should checkout main, pull the latest changes, and then
delete the feature branch:

```bash
~/src/git-workshop $ git checkout main
Switched to branch 'main'
Your branch is behind 'origin/main' by 1 commit, and can be fast-forwarded.
  (use "git pull" to update your local branch)

# Could optionally use the `--ff-only` flag here to be safer but in this case we know the pull will
# be a ff
~/src/git-workshop $ git pull
Updating 8adbc7b..5a91f76
Fast-forward
 first-feature.txt | 4 ++++
 1 file changed, 4 insertions(+)
 create mode 100644 first-feature.txt
Current branch main is up to date.

# Delete the local branch
# Use the -D flag to force delete a branch; the -d flag will stop you if Git things the content
# isn't merged back to the currently checked out branch
~/src/git-workshop $ git branch -D my-first-feature
Deleted branch my-first-feature (was 99f448c).

~/src/git-workshop $ gl
* 5a91f76 (HEAD -> main, origin/main) My first feature (#1)
* 8adbc7b Add content to helloworld.txt
* 3e228e1 Initial commit
```

This same procedure should be done on the "laptop" repo as well as any other copies of the repo.

## Recap

In this module you learned:

* How to visualize Git history from the command line
* What `HEAD` means in Git and what a detached HEAD state means
* How to create and checkout a new branch
* How to push a branch to a remote with an upstream reference
* How to clone a Git repository to a specific directory
* How to checkout a branch that already exists on the remote
* The difference between `git fetch` and `git pull`
* How to squash commits with `git rebase -i`
* How to force push a branch and warnings about the risks that come from force pushing
* How to hard reset a branch to fix history divergences
* How to create a PR on GitHub
* How to squash and merge a PR
* How to delete a local feature branch after merging a PR

Extra notes for `git rebase -i`

* Interactive rebases can also be used to edit commit messages for commits older than the most
  recent. This can be done independently of squashing.
  * To edit the most recent commit message (or change it in general) you can also use
    `git commit --amend`.
* Deleting a line from the rebase todo list will cause that commit to be dropped entirely.
