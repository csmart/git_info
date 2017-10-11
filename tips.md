<!-- vim-markdown-toc GFM -->

* [Tips](#tips)
	* [Enable autocorrect for Git commands](#enable-autocorrect-for-git-commands)
	* [Set aliases for Git commands](#set-aliases-for-git-commands)
	* [Show diffstat when rebasing](#show-diffstat-when-rebasing)
	* [Create and use backup branches](#create-and-use-backup-branches)
		* [Make a local backup branch](#make-a-local-backup-branch)
		* [Push your branch and use as a backup](#push-your-branch-and-use-as-a-backup)
		* [Push to a different remote branch](#push-to-a-different-remote-branch)
	* [Find parent commit which is common to multiple branches](#find-parent-commit-which-is-common-to-multiple-branches)

<!-- vim-markdown-toc -->

# Tips

Here are some tips that might help you with Git.

## Enable autocorrect for Git commands

If you find yourself often making typos when running Git commands, like _stats_
instead of _status_ or _chckout_ instead of _checkout_, Git can autocorrect
those for you automatically.

```bash
git config --global help.autocorrect 1
```

## Set aliases for Git commands

You can make your own shortcuts for various commands then you can just use the
shorthand.

```bash
git config --global alias.di diff
git config --global alias.ci commit
git config --global alias.co checkout
git config --global alias.st status
```

## Show diffstat when rebasing

If you want to see a diffstat of what changed after a rebase, set the following
config item.

```bash
git config --global rebase.stat true
```

## Create and use backup branches

If you're not confident with updating or rebasing your branch, you can always
reset it to some other point in time.

You could make a local or remote backup branch, or just reset to the latest
version on the remote if you've pushed your most recent changes.

### Make a local backup branch

Branches are really, really cheap and if you make a backup branch you can
always get back to what you had before you dug yourself into a hole you don't
know how to get out of!

```bash
# Make a backup branch
git branch mybranch-backup

# Do your rebase/merge/whatever

# Now if you get stuck, you can just reset your branch back to the backup!
git reset --hard mybranch-backup
```

### Push your branch and use as a backup

If you push your branch before you do something tricky like a rebase, then you
can just reset your local branch to what it was when you pushed it.

This is like an automated backup.

```bash
# Push your branch
git push origin mybranch

# Do your rebase/merge/whatever

# Reset your branch if you get stuck
git reset --hard origin/mybranch
```

### Push to a different remote branch

You can also just push to a different branch on the remote and use this as a
way to have backups.

```bash
# Push your local branch to a backup branch on the remote
git push origin mybranch:mybranch-remote-backup

# Do your rebase/merge/whatever

# Reset your branch if you get stuck
git reset --hard origin/mybranch-remote-backup
```

## Find parent commit which is common to multiple branches

Sometimes it's useful to find the common parent commit between to branches,
before they both went their own way. This can be any commit or branch, such as
local master and remote master.

```bash
git merge-base master origin/master
```

This will return the hash of the parent commit.
