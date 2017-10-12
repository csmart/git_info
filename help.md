# Common issues

Here are a few common issues that you might encounter when using Git. They
should provide some help on how to deal with these situations.

See the [tips section](/tips.md) for some ways you can help get yourself out of
sticky situations, like making backup branches (or just pushing your changes)
before you do something tricky.

<!-- vim-markdown-toc GFM -->

* [I've started making changes on master, not a branch](#ive-started-making-changes-on-master-not-a-branch)
* [I've added commits to my local master, not a branch](#ive-added-commits-to-my-local-master-not-a-branch)
* [I'm in detached HEAD state](#im-in-detached-head-state)
* [I did a pull (merge) instead of a pull --rebase](#i-did-a-pull-merge-instead-of-a-pull---rebase)
	* [I did not have to resolve conflicts..](#i-did-not-have-to-resolve-conflicts)
	* [I had to resolve conflicts..](#i-had-to-resolve-conflicts)
* [I'm stuck resolving a conflict](#im-stuck-resolving-a-conflict)
* [I squashed commits too far back via rebase](#i-squashed-commits-too-far-back-via-rebase)

<!-- vim-markdown-toc -->

# I've started making changes on master, not a branch

Don't panic!

If you have not committed any of your changes, all you need to do is make a
branch.

Your changes in the workspace will still be there and you can continue to work
on them and/or commit them.

```bash
# Create a new branch
git checkout -b saveme

# Check status to see all your changes still there
git status

# Confirm the changes are still there
git diff

# Keep working, or just commit your changes.
git commit -a

```

# I've added commits to my local master, not a branch

Don't panic!

If you have already committed your code, it's easy!

Just make a new branch and your commits will be there. Then we can reset your
local master to the latest code on the remote master branch, which will remove
your local commits.

```bash
# Make a new branch from current master
git branch savemeagain

# Reset master back to the remote, loses commits
git reset --hard origin/master

# Switch to your branch and keep working!
git checkout savemeagain
```

# I'm in detached HEAD state

This is because your workspace is not a tracked branch, perhaps as a result of
a rebase.

You can simply switch back to a branch, or create a branch and commit your
work.

Have a look at _git status_ to see what's going on.


# I did a pull (merge) instead of a pull --rebase

We do a rebase because we want to avoid merge commits that have to resolve
conflicts. We want conflict resolution to be handled as part of the regular
commits in your branch.

## I did not have to resolve conflicts..

If you *did not* have to resolve any conflicts you can just do a rebase. Git
is smart enough to know that the commits are already there and will fix up the
order.

```bash
git rebase origin/master
```

## I had to resolve conflicts..

If you *have* resolved some conflicts, this is more tricky - we do not want to
have those merge conflict commits.

The best way is to reset your local branch back to the changes before you did
the pull.

If you can't do that because you haven't pushed or made a backup, you could
create a new branch and cherry-pick your commits.

```bash
# Rename your broken branch
git branch --move mybranch mybranch-broken

# Create a new branch from master
git checkout -b mybranch master

# Get a list of your commits from your broken branch
git log mybranch-broken

# Cherry pick commits from your broken branch onto your new branch
git cherry-pick <commits>

# Do a pull --rebase
git pull --rebase origin/master
```

# I'm stuck resolving a conflict

If you're stuck in a merge or rebase conflict that you don't know how to fix,
you can always back out of the merge. This will put you back on your local
branch before you did the merge or rebase.

```
# Back out of a merge
git merge --abort

# Back out of a rebase
git rebase --abort
```

# I squashed commits too far back via rebase

This can happen if you have done a rebase and accidentally squashed a commit
that was already part of the upstream master branch.

This is trickier to fix because you've now re-written the history and have the
same change in two commits, along with extra changes in your local one.

Furthermore, you may have lost merge commits unless you use the
_--preserve-merges_ option.

*Your best bet is to reset to a backup or remote copy of the branch.*

If you can't do that, then you can try making a new branch from master and
cherry pick the original commits that you squashed and fix up the conflict.

