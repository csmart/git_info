# Git

<img src="/images/git_2x.png" align="right" width="400" alt="If that doesn't 
fix it, git.txt contains the phone number of a friend of mine who understands 
git. Just wait through a few minutes of 'It's really pretty simple, just think 
of branches as...' and eventually you'll learn the commands that will fix 
everything. https://xkcd.com/1597/"/>

<!-- vim-markdown-toc GFM -->

* [TLDR; Just show me how to use Git!](#tldr-just-show-me-how-to-use-git)
* [About Git](#about-git)
	* [Additional resources](#additional-resources)
* [Development methodology](#development-methodology)
* [Installing Git](#installing-git)
* [Setting Git defaults](#setting-git-defaults)
* [Creating a repository](#creating-a-repository)
* [Getting code from an existing repository](#getting-code-from-an-existing-repository)
* [Working with Git](#working-with-git)
	* [Workspace - where you modify code](#workspace---where-you-modify-code)
	* [Staging area - where you prepare commits](#staging-area---where-you-prepare-commits)
	* [refs (references) - pointers to a commit](#refs-references---pointers-to-a-commit)
	* [HEAD - points to the branch in workspace](#head---points-to-the-branch-in-workspace)
* [Remotes](#remotes)
	* [Referencing code directly in remotes](#referencing-code-directly-in-remotes)
	* [Adding a remote](#adding-a-remote)
* [Branches](#branches)
	* [Local and remote branches](#local-and-remote-branches)
		* [Branch tracking information](#branch-tracking-information)
	* [Creating a local branch](#creating-a-local-branch)
		* [Creating backups](#creating-backups)
	* [Renaming a branch](#renaming-a-branch)
	* [Switching between branches](#switching-between-branches)
* [What history looks like](#what-history-looks-like)
	* [Viewing the history](#viewing-the-history)
	* [Graphical log viewer](#graphical-log-viewer)
	* [View a commit](#view-a-commit)
* [Making changes to the code](#making-changes-to-the-code)
	* [Adding a file](#adding-a-file)
	* [Making a change](#making-a-change)
		* [Viewing differences in the code](#viewing-differences-in-the-code)
	* [Committing a change](#committing-a-change)
		* [Writing a good commit message](#writing-a-good-commit-message)
		* [Amending a commit](#amending-a-commit)
* [Modifying and squashing commits](#modifying-and-squashing-commits)
	* [Interactive rebase](#interactive-rebase)
		* [Delete commits](#delete-commits)
		* [Reorder commits](#reorder-commits)
		* [Squashing commits](#squashing-commits)
		* [Modifying a commit message](#modifying-a-commit-message)
		* [Modifying a commit](#modifying-a-commit)
* [Getting recent updates](#getting-recent-updates)
* [Sharing your changes](#sharing-your-changes)
	* [When pushing fails](#when-pushing-fails)
		* [Forcing a push](#forcing-a-push)

<!-- vim-markdown-toc -->

# TLDR; Just show me how to use Git!

This document is designed to provide just enough information to help you
_understand_ the basics of Git.

To jump to a quick guide on how to use Git, see the [workflow
example](/workflow.md).

# About Git

[Git](https://git-scm.com/) is a Source Code Management (SCM) system, similar
to other tools like Subversion, which helps you to keep track of your code over
time. Git supports common SCM features like branching, committing, merging and
tagging.

There are some significant differences between Git and Subversion however, and
it will take some time to get used to them. Hang in there though, the rewards
are worth it!

The biggest core difference is that Git is _distributed_, not _centralised_.

With Subversion, there is a single repository where the code and its history is
stored. No-one gets full direct access to the repository (except
administrators).  All SCM related commands like committing, branching, merging
and even getting the log, are run across the network and on the central server.

With Git on the other hand, while there is generally a single blessed
repository sitting on a server somewhere (perhaps a project's GitLab account),
every user gets a full local copy of the repository by default. Each copy is
equal.

Commands like committing, branching, merging and even getting the log are run
_locally_. Git does not touch the network unless you tell it to with one of a
few commands.

* clone (get a local copy of a remote repository)
* fetch (get the latest changes from a remote repository)
  * pull (wraps fetch then does a merge)
  * remote update (wraps fetch to get updates from all remotes)
* push (push my local changes to a remote repository)

Why does that matter? It grants the user flexibility that is not otherwise
available, as you'll see below.

## Additional resources

The Git website hosts the official open source [Pro Git
book](https://git-scm.com/book/) and
[Tutorial](https://git-scm.com/docs/gittutorial), as well as a number of
[useful links](https://git-scm.com/documentation/external-links).

If you're keen to try out Git in an online interactive tutorial, have a look at
the [one from GitHub](https://try.github.io/).

This repo has a [cheatsheet available](/cheatsheet.md) which provides many
common Git commands, as well as a page on the [basic internals of
Git](/internals.md), if you are interested.

# Development methodology

Git is very powerful and while it does not dictate a particular way of
working, there are some established best practices which will be very helpful.
These will be especially useful when working with other developers and
maintaining quality code.

A common development approach is to perform all changes in a feature branch.
Once the changes are ready, generally following some sort of automated testing
and code review, they are merged into the main code which is stored in the
_master_ branch (which is like the _trunk_ branch in Subversion).

Following this kind of model, developers do not commit directly to the master
branch. The idea behind this is that only approved, quality code lands in the
main branch that everyone else consumes.

Branching is core to the fundamental design of Git, it was not added on at a
later stage.

The changes in a feature branch should have a neat commit history, with few
commits that are grouped together as appropriate. For example, aim to have a
single commit for a feature, rather than dozens. This makes the history much
more clean and easier to work with in the future.

# Installing Git

Git is available for most platforms, including
[Linux](https://git-scm.com/download/linux),
[Windows](https://git-scm.com/download/win) and
[MacOS](https://git-scm.com/download/mac).

See the appropriate link above for your platform (but we'll assume you're using
Linux).

# Setting Git defaults

Once you have Git, you should set some global default settings. Setting these
can be done by running the config command which writes to the .gitconfig file
in your home directory (on Linux at least). You can also just edit that file
directly, if you know what settings to change (see _git config --help_).

Note that these settings can be overridden on a per-repository basis by
omitting the _--global_ option when run inside the workspace.

```bash
# Tell Git the default name to use
git config --global user.name "Full Name"

# Tell Git the default email address to use
git config --global user.email "email address"

# Colorise Git output
git config --global color.ui auto

# Set vim as the default editor
git config --global core.editor /usr/bin/vim

# Use 16 chars for short commit hashes
git config --global core.abbrev 16
```

# Creating a repository

Git is distributed and anyone can create a local repository.

Simply provide the name of a directory which you wish to turn into a Git repo
(this can already have files in it) and initialise it!

```bash
git init /path/to/directory
```

The directory is now an empty repository, as nothing is being tracked by Git
yet. We will see how to add and commit files soon.

For those who are curious, your Git repository actually lives in a hidden
directory _.git_ in this parent directory. If you delete this directory, you
will lose your Git history!

# Getting code from an existing repository

Often you aren't creating your own local repository but instead want a copy of
one that already exists. This follows a different process.

With Subversion, you get a copy of the code at a particular point in time via
the _checkout_ command which is run against the central server. This is not the
full history, just the code at that point in time and is generally a branch or
a tag. Each time a developer wants a different tag or branch, they perform a
checkout again into a different directory.

With Git however, you _clone_ just one copy of the entire repository from a
remote location. This is generally from a server (like GitLab) via SSH, but
can be via other protocols like Git, HTTPS, or even a local file system.

The method you use to clone the repository will depend on what protocols the
server provides.

```bash
# This uses SSH
git clone user@server:/path/to/code /path/for/local/code

# This uses HTTPS
git clone https://user@server/path/to/code /path/for/local/code
```

# Working with Git

Git has the following core concepts which are useful to know.

| Concept | Purpose |
| --- | --- |
| workspace | Local directory where you modify files in the branch you have checked out. |
| staging area | Virtual place where you prepare commits before committing them. |
| refs (references) | Point branches and tags to a commit. |
| HEAD | A special reference to the latest commit for the branch in workspace. |

## Workspace - where you modify code

While a clone gets a full copy of the repository, by default Git will checkout
a copy of the _master_ branch into the directory specified at the end of that
command.

This is called your _workspace_ and is where you access and modify the contents
of the branch.

If you initialised a new repository in your current local directory, you're
already in your workspace. Else, you need to _cd_ into that directory.

You can check the status of your workspace by running the status command.

```bash
git status
```

This will print the branch and commit information, show you any files which are
not tracked, and any files which have been changed but not committed.

We will see how to manage branches shortly.

## Staging area - where you prepare commits

Before you can _commit_ any modified files in your workspace, they must be
added to the _staging area_ in the Git repository.

This is a special Git feature that lets you craft what a commit will look like
from the files you've modified. When you do a commit, only changes which have
been staged will be committed.

This lets you perform multiple and/or different changes, but group them in
separate commits.

To put a file into the staging area, you _add_ it.

```bash
# Add README.me to the staging area
git add README.md
```

You can even add just _parts_ of a changed file, in selectable chunks. This can
be useful when you've made lots of changes in the workspace but don't want them
all to go into the current commit.

```bash
# Add just _parts_ of a changed file
git add --patch README.md
```

You can see which files you've staged with the _status_ command, and see the
changes with _diff --cached_ command.

## refs (references) - pointers to a commit

In Subversion, a branch or tag is a full copy of the repository (usually done
with hardlinks).

In Git, a branch or tag just references a commit in the project history (this
is why branching is so cheap!).

These refs files are usually the name of a branch or tag and are stored under
the _.git/refs/_ directory in the repository.

So when you refer to a branch like _master_ this is actually just a pointer to
a commit. Git knows which commit by reading the refs file called _master_.

```bash
# See the content of the refs file for local master branch
cat .git/refs/heads/master

# Use Git to parse the ref for local master branch
git rev-parse refs/heads/master
```

## HEAD - points to the branch in workspace

There's a special reference in Git called _HEAD_. It points to the latest
commit on the branch you have checked out in your workspace.

You can use this to refer to the latest commit in your workspace when
performing Git commands, or commits relative to HEAD.

| Reference | Location |
| --- | --- |
| HEAD | The latest commit |
| HEAD^ | One commit back from HEAD |
| HEAD^^ | Two commits back from HEAD |
| HEAD~9 | Nine commits back from HEAD |

```bash
# See what branch HEAD is pointing to (probably master)
git symbolic-ref HEAD

# We can parse the ref and see what commit HEAD is pointing to
git rev-parse HEAD
```

# Remotes

**This is where Git _really_ starts to differ from other tools like Subversion.**

In Subversion there is only one remote. You don't even really think about it
because any operations must automatically talk to the remote server.

Git is distributed and it's not tied to a remote server. It lets you talk to
_any number of remotes in different locations_ that have a copy of the same
code base.

If you cloned a repository rather than initialising one locally, Git will
remember the location that you initially cloned from.

This is called a _remote_ and Git gives it the name _origin_.

See your remotes with the following command:

```bash
git remote -v
```

You might want to use more than one remote if you have cloned your own copy of
the code from a local server when there's also an upstream version elsewhere.

Because you can have more than one, _Git needs to know which remote you want to
talk to_ when you perform a network operation.

More about that soon!

## Referencing code directly in remotes

Furthermore, you can also _reference remotes directly_ when you want to look at
code.

* master - this is your local master branch
* origin/master - this is the master branch on the remote called origin

Your local branch master is completely separate entity to the master branch on
the remote. You can have local commits on your master that do not exist on any
of the remote master branches.

If you want to refer to the latest code on the remote, then use the remote name
prefix like origin/master.

## Adding a remote

Adding a second remote pointing to the upstream version would let you fetch the
changes from upstream and merge them into your local repository. This is part
of the power of distributed SCM and it might take a while to get your head
around.

You can easily add another remote, but the name must be unique (i.e. it can't
be called _origin_).

```bash
git remote add upstream user@another-server:/path/to/code
```

You should be able to list your remotes again as above and see two.

This is useful to know because many network based Git commands need to know
which remote server you want to talk to. Most of the time this will be
_origin_.

More on that soon.

# Branches

In Subversion, you have a one to one relationship between the code on the
server and the local copy of it in your workspace. You check out one branch or
tag into one local directory.

In Git, remembering that you have a full copy of the repository, you _switch_
between branches in the same workspace.

This is a very big difference in the way Git and Subversion work and it's
something that will take some getting used to.

By default your workspace will have a checked out copy of the _master_ branch.

## Local and remote branches

In Subversion, branches only really exist on the remote server.

In Git, branches exist in both your local and the remote repositories, although
you might never have actually checked out a branch locally. Due to Git's
distributed nature however, you can also have your own local branches which are
not on the remote.

Remember that any branching or merging is a local affair unless you push your
changes to a remote!

Note that you can see some extra information about the branch if you specify
the --verbose option, like the latest commit on the branch.

```bash
# See local branches
git branch --verbose

# See available remote branches
git branch --verbose --remotes

# See all branches:
git branch --verbose --all
```

### Branch tracking information

Remember how in Git you can have multiple branches and multiple remotes? Enter
(optional) tracking!

Tracking information specifies which branch on which remote server your local
copy of the branch is set to follow by default. This means if you told Git to
just push or fetch the latest changes without specifying the remote or branch,
it will do so from the branch it is tracking.

You can see the tracking information for the local branch you're in by adding
some extra verbosity to the branch command!

```bash
git branch --verbose --verbose
```

## Creating a local branch

Creating a local branch is as simple as telling Git what name you want to give
the branch and where it should branch from (by default this is from the branch
you currently have checked out).

For example, you can create a branch called _mybranch_ from the tip of your
local master branch like so.

Note that if your local master is behind you may want to [update it
first](#getting-recent-updates) to get the latest changes from the remote
branch.

```bash
git branch mybranch master
```

It may be better to create your branch from the latest code on the remote, as
your local branch may not be up-to-date or have different commits.

Simply add the remote name prefix.

```bash
git branch mybranch origin/master
```

Branches are extremely cheap because they are actually just a file which points
to a unique commit in the Git history. No duplication of files is done.

### Creating backups

Because branches are so cheap, it's a really nice way to create a local backup
of a branch. This way you can always get your original branch back to the way
it was before you tried something tricky, like a _rebase_.

```bash
git branch mybranch-backup mybranch
```

## Renaming a branch

Given that a branch is just a file which points to a commit, renaming branches
is done by simply moving the branch to a new name.

```bash
git branch --move oldbranch newbranch
```

## Switching between branches

Remember that Git has a full copy of the repository but only one copy of the code
is accessible at any point in time in your workspace. By default this is the
master branch, but we've shown how you can create as many branches as you like.

We also mentioned that unlike Subversion, you just switch your workspace
between branches.

This is done with the _checkout_ command.

```bash
git checkout mybranch
```

This will cause your workspace to switch the contents from the current branch
to the new branch you specified.

Everything is changed on disk automatically.

If your workspace is clean, you should not have any trouble switching between
branches.

If you have made changes to your local files but not yet committed them, those
changes will be maintained in your workspace when you switch branches.

Note however, that there might be conflicts if the changes you've made are
drastically different to those in the other branch. In this case, you just need
to fix up any conflicts.

# What history looks like

You will just see your repository as a series of commits with a bunch of
associated data. If you have more than one branch, you'll see that information
too. The history looks a lot like it does in any other SCM tools.


A flat history of the master branch will look something like this, with oldest
commit at the bottom.

```bash
* 602083f0da6aacac (HEAD -> master) Second set of changes
|
* 22b56bb62019c7b8 First set of changes
|
* 0d5dbcf6eac9a558 Initial commit in master branch
```

Generally a history where work is done in branches and merged to master will
look something like this.

```bash
*   cef3f6b8bab5a556 (HEAD -> master) Merge branch 'otherbranch'
|\
| * 4c05b05ba3e0fda6 (otherbranch) Second set of changes in otherbranch
|/
*   b1d3234a14c1c8b8 Merge branch 'mybranch'
|\
| * d9b81ddb5636ba24 (mybranch) First set of changes in mybranch
|/
* 0d5dbcf6eac9a558 Initial commit in master branch
```

## Viewing the history

Similar to Subversion, you can view the log (or history) of the repository.
This can be the full history, or just a range. By default this is run against
the branch you have checked out in your workspace.

```bash
# Full commit messages
git log

# Get log in a range
git log <commit>...<commit>
```

However you can also check the log from any branch, local or remote, or even
from a specific commit in the history.

```bash
git log origin/master
```

Git also lets you do lots of fancy, tricky things with the log too, like
showing all commits by a specific author.

```bash
# Commits by name
git log --author="Full Name"

# Commits by email address
git log --author="email address"
```

Git supports a [pretty format](https://git-scm.com/docs/pretty-formats)
that has some options like _oneline_, _short_ and _stat_.

```bash
# Statistics with the commit, like number of lines changed
git log --stat

# One line commit messages
git log --oneline
```

It can also graph the relationship between all branches, and decorate the
output.

```bash
# Graph relationship between all branches
git log --oneline --graph --decorate --all
```

Git also supports custom _formats_ to show anything you like, which you can
combine with options like _graph_.

* Show the branch interactions
* Commit hash
* Branch a commit came from
* One line commit message summary
* How long ago the commit was made
* Who made the commit

```bash
git log \
--graph \
--abbrev-commit \
--pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset'
```

Git log is really powerful.

## Graphical log viewer

Sometimes you might want a graphical view of the history. GitLab can do this
for you or you can use command line tools like [gitk (open
source)](https://git-scm.com/docs/gitk) or [GitKraken
(proprietary)](https://www.gitkraken.com/).

## View a commit

You can view any number of commits by using the _show_ command with one or more
commit hashes. This will show you the change itself and accompanying metadata
such as commit message, author and date.

```bash
git show <hash> [<hash>]
```

# Making changes to the code

So now we're ready to change the code.

## Adding a file

Before you can commit a change however, Git must be tracking the file. Like
Subversion, this is an _add_ command.

```bash
git add file
```

From now on, Git will track the contents of this file and include it in the
commit.

Unlike Subversion, each time you want to commit a change, the file must be
re-added. This is because Git has an awesome feature called the staging area
where you can gather up changes before they are committed.

This allows you to do things like add specific _parts_ of a larger change into
a commit, rather than the entire change.

We'll re-visit this some more below.

## Making a change

You can make changes to tracked files in your workspace.

### Viewing differences in the code

You can view the differences between any two points in the Git history or
workspace.

By default, the _diff_ command will compare your workspace with the latest
commit in the branch you're on.

```bash
git diff
```

If you have added some files to the staging area, you won't see these unless
you add the --cached option.

```bash
git diff --cached
```

It gets more interesting than that, you can compare your workspace with the
latest changes in a branch (including remotes) or with a commit at any point
in time.

```bash
# Compare workspace with remote branch
git diff remote/branch

# Compare workspace with another commit
git diff <commit>

# Compare two branches
git diff branch other/branch
```

## Committing a change

When you commit a change using Subversion, it is a centralised event.
You make a change to a file and when you commit it, the change is sent to the
server and a commit record is made. This change is then inherited by everyone
else using that branch in Subversion.

Commits in Git are local. They are not sent to a remote server unless those
commits are pushed.

To make a change, just edit a file in your workspace that Git is tracking (has
previously been added).

The status command should show that your workspace has a change that is "not
staged for commit."

```bash
git status
```

To stage them, as discussed above we _add_ them.

```bash
git add file
```

Now status should tell you there are "changes to be committed."

Next, you can commit these changes which will prompt you for a commit message.

```bash
git commit
```

Type in your commit message. If you need to change the default text editor, you
can set this in your ~/.gitconfig.

### Writing a good commit message

Quality, informative commit messages are really valuable.

<img src="/images/git_commit_2x.png" width="400" alt="Merge branch 
'asdfasjkfdlas/alkdjf' into sdkjfls-final https://xkcd.com/1296/"/>

In general, good commit messages should follow a pattern like so.

- Start with a short description of the problem, less than 50 chars. Ideally
  this should also reference the area of the code the change applies to.
- In a new paragraph, describe the problem or feature the patch is addressing
  using lines of no more than 72 characters.
- In a new paragraph, describe how the patch solves or implements the above.
- Add any other notes relating to the patch (like how to test it).
- If related tickets, merge requests or snippets exist in GitLab, they can be
  referenced.

Here is an example commit message.

```
readme: added authors to the readme

There were other authors that were contributing to this code, however they were
left off the AUTHORS section in the README.md.

This patch adds these authors so that their work is attributed.

Related to #123, fixes #456 and closes group/otherproject#22.

See merge request !123 and related snippet $123.
```

The first line summary is particularly useful because Git lets you format logs
on just one line, which is helpful for quickly finding a commit.

### Amending a commit

If you've made a commit but later you wish to modify it (without making a new
commit on top) you can amend it. This is generally not a good idea if you are
working collaboratively on a branch as it will re-write the history of the
project (the commits will change and be different for other developers).

The only difference between this and making a regular new commit, is adding the
_--amend_ option to the _commit_ command.

So simply make your changes as usual, add the file (or part thereof) and then
when you run the _commit_ add the _--amend_ option.

```bash
git commit -a --amend
```

Note that this will re-write the history. The new change will be squashed into
the new commit and you will have a new commit hash.

This means that if you try to push the change, Git will warn you that the
histories have diverged and suggest you do a pull to get the latest changes.

If you're amending, you don't want to do that, you just want to force push.

# Modifying and squashing commits

<img src="/images/danger.jpg" align="left" width="200" alt="Danger, Will
Robinson, Danger!"/>

Git lets you modify your existing commits in a number of ways, via an
interactive _rebase_ command. This is one of the most amazing, useful, yet
often misunderstood features in Git.

* p, pick = use commit
* r, reword = use commit, but edit the commit message
* e, edit = use commit, but stop for amending
* s, squash = use commit, but meld into previous commit
* f, fixup = like "squash", but discard this commit's log message
* x, exec = run command (the rest of the line) using shell
* d, drop = remove commit

It can easily get you into trouble as it will change and re-write your branch
history, so consider [creating a backup
branch](/tips.md#make-a-local-backup-branch) before you use rebase.

**WARNING:** In general, you should not change the history for branches that
have already been made public, in particular the master branch. This will break
everyone else's copy of the branch and create problems for any other feature
branches that are in progress.

However, rebase is a really great tool for your own feature branches. It
provides a way to group and tidy up commit history before your branch is merged
into master.

## Interactive rebase

Before you can change history, you need to perform an interactive rebase and
provide Git with a commit range.

For example, you go from HEAD (the latest commit on the branch in your
workspace) back two commits, you could do this.

```bash
git rebase --interactive HEAD~2
```

This will put you into a menu where you can see the three commits listed with
options for the rebase command you want to perform (see above).

### Delete commits

To delete a commit you can simply delete the line, then save and exit the
rebase file.

Alternatively, change _pick_ to _drop_, then save and exit the rebase file.

### Reorder commits

A simple rebase task is to simple re-order the commits. All you need to do is
move the lines into the new order you want.

Keep in mind that you may encounter conflicts which you need to fix. There may
be code missing which means that Git can't apply the patch.

### Squashing commits

You can squash multiple commits together and keep their commit messages by
finding the first commit you want to keep and leaving it at _pick_.

Now go to the next commit(s) you want to squash together and change _pick_ to
_squash_.

Save and close the rebase file and Git will prompt you for a new commit
message. By default this will be the combination of the commits. Once you're
happy with your new combined commit message, save and quit the file and Git
will squash the commits together into one.

If you are happy to discard any of the other commit message you can use _fixup_
instead of _squash_ and they will be automatically discarded.

### Modifying a commit message

Change _pick_ to _reword_ for the commit message(s) you want to re-write then save
and close the rebase file.

You can then edit each commit message one by one.

### Modifying a commit

Change _pick_ to _edit_ for the commit(s) you want to edit then save and
close the rebase file.

Git will take your workspace back to each commit in turn.

Modify files as you see fit, add them, then continue the rebase.

```bash
vim README.md

git add README.md

git rebase --continue
```

# Getting recent updates

When you're working on a branch that is going to go into master, often master
will move ahead before you're ready to merge your new changes in.

While you can keep working, if you ignore the changes in master, you may get
conflicts when you try to merge the branch into master later.

To get the latest changes in, you can do two things, merge or rebase.

If you're working on your own branch, rebase is the cleanest method as it takes
your changes off, adds the new commits from master and replays your changes
back on top.

This way it's like you just branched from master today!

If you have to fix up any conflicts, these just become part of your series of
changes, not a special merge commit later that goes onto master. This makes
life much easier when working on code with others.

```bash
git pull --rebase origin master
```

This also works if you're working on a feature branch collaboratively. If
someone has made a change to your branch, just rebase their changes onto yours,
and push.

# Sharing your changes

Once you have committed local changes to a branch, you can push it to a
remote server.

Generally you should tell Git which remote you want to push to and which branch
(even though you have a local branch checked out, you might not want to push
it!).

```bash
git push origin mybranch
```

If you want to set or update the tracking information for your local branch to
the remote location you are pushing, you can tell it to do so.

```bash
git push --set-upstream origin mybranch
```

Once your branch is set up to track a remote branch you can leave those details
off.

```bash
git push
```

## When pushing fails

If other users have committed changes to the branch you are pushing to the
remote copy of the branch will have extra commits that your local copy does not
have.

If you try to push your branch, Git will report an error because there are
diverged paths. That is to say, your local branch has different commits to the
remote, even though they share a common parent commit.

Git will ask you to deal with this and try your push again.

You have two main choices:

 - Fetch and merge in the changes that exist on the remote server.
 - Fetch the changes that exist on the remote server and replay your changes on
   top.
 - Replace the remote changes with your own (this removes those remote commits).

Git will recommend that you do a pull to get the most recent changes. This is
the most common way to pull in changes that have gone upstream.


### Forcing a push

Generally, if you are working on your own branch and have done a rebase from
another branch, or amended a commit you're safe to force push.

If you are working collaboratively, then you need to consider whether force
pushing is correct - it may remove genuine changes other developers have
pushed.

To force a push add the --force command line option to the push command:

```bash
git push --force
```

