
<!-- vim-markdown-toc GFM -->

* [Information about Git](#information-about-git)
	* [Additional resources](#additional-resources)
* [Development methodology](#development-methodology)
* [Installing Git](#installing-git)
* [Settings Git defaults](#settings-git-defaults)
* [Creating a repository](#creating-a-repository)
* [Getting code from an existing repository](#getting-code-from-an-existing-repository)
* [Workspace](#workspace)
* [Remotes](#remotes)
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
	* [Committing a change](#committing-a-change)
		* [Adding a commit message](#adding-a-commit-message)
		* [Amending a commit](#amending-a-commit)
* [Getting recent updates](#getting-recent-updates)
* [Viewing differences in the code](#viewing-differences-in-the-code)
* [Sharing your changes](#sharing-your-changes)
	* [When pushing fails](#when-pushing-fails)
		* [Forcing a push](#forcing-a-push)

<!-- vim-markdown-toc -->

# Information about Git

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
administrators).  All SCM related commands like, committing, branching, merging
and even getting the log, are run across the network and on the central server.

With Git on the other hand, while there is generally a single blessed
repository sitting on a server somewhere (perhaps a project's GitLab account),
every user gets a full local copy of the repository by default. Each copy is
equal.

Commands like committing, branching, merging and even getting the log are run
_locally_. Git does not touch the network unless you tell it to with one of the
following three commands:

* clone (get a local copy of a remote repository)
* fetch (get the latest changes from a remote repository)
* push (push my local changes to a remote repository)

Why does that matter? It grants the user flexibility that is not otherwise
available, as you'll see below.

## Additional resources

The Git website hosts the official open source [Git Pro
book](https://git-scm.com/book/) and
[Tutorial](https://git-scm.com/docs/gittutorial), as well as a number of
[useful links](https://git-scm.com/documentation/external-links).

If you're keen to try out Git in an online interactive tutorial, have a look at
the [one from GitHub](https://try.github.io/).

There is a [cheatsheet available](/cheatsheet.md) which provides many common
Git commands.

Finally, there is a page on the [basic internals of Git](/internals.md) if you
are interested.

# Development methodology

Git is very powerful and while it does not dictate a particular way of
working, there are some established best practices which will be very helpful.
These will be especially useful when working with other developers and
maintaining quality code.

A common development approach is to perform all changes in a feature branch.
Once the changes are ready, generally following some sort of automated tests
and code review, they are merged into the main code which is stored in the
_master_ branch (which is like the _trunk_ branch in Subversion).

Following this kind of model, developers do not commit directly to the master
branch. The idea behind this is that only approved, quality code lands in the
main branch that everyone else consumes.

Branching is core to the fundamental design of Git, it was not added on at a
later stage.

# Installing Git

Git is available for most platforms, including
[Linux](https://git-scm.com/download/linux),
[Windows](https://git-scm.com/download/win) and
[MacOS](https://git-scm.com/download/mac).

See the appropriate link above for your platform (but we'll assume you're using
Linux).

# Settings Git defaults

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

Simply enter a directory which you wish to turn into a Git repo (this can
already have files in it) and initialise it!

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
could use other protocols like Git, HTTP, or even a local file system directly.

The method you use to clone the repository will depend on what protocols the
server provides.

```bash
# This uses SSH
git clone user@server:/path/to/code /path/for/local/code

# This uses HTTP
git clone https://user@server/path/to/code /path/for/local/code
```

# Workspace

While a clone gets a full copy of the repository, by default Git will checkout
a copy of the _master_ branch into the directory specified at the end of that
command. This is called your _workspace_ and is where you access and modify the
contents of the branch.

If you initialised a new repository in your current local directory, you're
already in your workspace. Else, you need to _cd_ into that directory.

You can check the status of your workspace by running the status command.

```bash
git status
```

This will print the branch and commit information, show you any files which are
not tracked, as well as files which have been changed but not committed.

We will see how to manage branches shortly.

# Remotes

If you cloned a repository rather than initialising one locally, Git will
remember the location that you initially cloned from.

This is called a _remote_ and Git gives it the name _origin_. You can see this
with the following command:

```bash
git remote -v
```

As you might have guessed, Git can have multiple remotes pointing to the same
code base but at different locations.

You might want to do this if you have cloned your own copy of the code from a
local server when there's also an upstream version elsewhere.

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

```bash
git branch mybranch master
```

Branches are extremely cheap because they are actually just a file which points
to a unique commit in the Git history. No duplications of files is done.

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

Remember that Git has a full copy of the repository but only copy of the code
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

Git also supports custom _format_ to show anything you like, which you can
combine with options like _graph_.

* Show the branch interactions
* Commit hash
* Branch commit came from
* One line commit message summary
* How log ago the commit was made
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
where you can gather ups of changes before they are committed.

This allows you to do things like add specific _parts_ of a larger change into
a commit, rather than the entire change.

We'll re-visit this some more below.

## Committing a change

When you make a commit a change using Subversion, it is a centralised event.
You make a change to a file and when commit it, the change is sent to the
server and a commit record is made. This change is then inherited by everyone
else using that branch in Subversion.

Commits in Git are local. They are not sent to a remote server unless those
commits are pushed.

To make a change, just edit a file in your workspace that Git is tracking (has
previously been added).

The status command should show that your workspace has a change that are "not
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

Type in your commit message. If you need to set this to a different edit, you
can set this in your ~/.gitconfig.

### Adding a commit message

In general, good commit messages should follow a pattern like so.

- Start with a short description of the problem, less than 70 chars. Ideally
  this should also reference the area of the code the change applies to.
- In a new paragraph, describe the problem or feature the patch is addressing.
- In a new paragraph, describe how the patch solves or implements the above.
- Add any other notes relating to the patch (like how to test it).
- If related tickets, merge requests or snippets exist in GitLab, they can be referenced.

Here is an example commit message.

```
readme: added authors to the readme

There were other authors that were contributing to this code, however they were
left off the AUTHORS section in the README.md.

This patch adds these authors so that their work is attributed.

Related to #123, fixes #456 and Closes group/otherproject#22.

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
git pull --rebase origin/master
```

This also works if you're working on a feature branch collaboratively. If
someone has made a change to your branch, just rebase their changes onto yours,
and push.

# Viewing differences in the code

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

If other users have committed changes to your branch, the remote copy of the
branch will have extra commits that your local copy does not have.

If you try to push your branch, Git will error because there are diverged
paths. That is to say, your local branch has different commits to the remote,
even though they share a common parent commit.

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
another branch, or amended a commit you're save to force push.

If you are working collaboratively, then you need to consider whether force
pushing is correct - it may remove genuine changes other developers have
pushed.

