
<!-- vim-markdown-toc GFM -->

* [Common Git workflow](#common-git-workflow)
	* [Getting and working in a repository](#getting-and-working-in-a-repository)
	* [Getting the latest changes from master into feature branch](#getting-the-latest-changes-from-master-into-feature-branch)
	* [Merging your code back into master](#merging-your-code-back-into-master)
		* [Pushing code to GitLab](#pushing-code-to-gitlab)
		* [Pushing code for Gerrit code review](#pushing-code-for-gerrit-code-review)
			* [Using Git review with Gerrit](#using-git-review-with-gerrit)
	* [Starting on the next branch](#starting-on-the-next-branch)
		* [Reset your master branch](#reset-your-master-branch)
* [Common issues](#common-issues)

<!-- vim-markdown-toc -->

# Common Git workflow

Here is what a standard branch based development workflow using Git might look
like. It does not go into all details or options, it's designed to give you a
quick guide on how you might use Git.

## Getting and working in a repository

```bash
# Clone a repository from a remote location over SSH
git clone user@server:/project ~/code/project

# Go into your workspace
cd ~/code/project

# Check the status, note you're in the master branch
git status

# Check the log
git log

# Create a new feature branch from master
# _origin/master_ branches from the latest code on the remote master branch
# _master_ would branch from the state of your local master branch
git branch mybranch origin/master

# Switch to the new branch
git checkout mybranch

# Edit a file
vim README.md

# Look at your change
git diff

# Add the changes to the staging area
git add README.md

# Commit your change
git commit

# Show the change and other details of your commit
git show

# Amending a commit
vim README.md
git add README.md
git commit --amend
```

Note that amending a commit will re-write your history as the change is
different now and the commit hash will be different.

## Getting the latest changes from master into feature branch

We can pull in the latest changes from the master branch into your local
feature branch.

This is done as a fetch and _rebase_ not a fetch and _merge_. This makes your
new changes sit _directly on top of the latest code in master_, as though you
had branched today.

Any conflicts are fixed as a part of your local commits, not as a special merge
commit. This helps us avoid merge problems down the track when we merge back
into master.

```bash
# Pull in the latest changes from master into feature branch
git pull --rebase origin master

# Fix any conflicts here
git status
vim README.md
git add README.md
git rebase --continue
```

Note that this will _re-write your recent history_.

All of the hashes from your commits will be different now because we've
inserted any new commits from master in before your local commits.

## Merging your code back into master

Now that you have made your change, you need to follow a process to get the
code in your feature branch merged into master.

### Pushing code to GitLab

With GitLab you push your local branch up to the remote server and then create
a merge request in the web interface.

```bash
# Push your branch to GitLab and set branch tracking information
git push origin mybranch --set-upstream
```

If you get an error saying there is new code on the server that you don't have,
Git will recommend that you do a _pull_ to merge in those changes.

However, if the code is different because you have _re-written your history_,
*do not do a git pull*. You already have the latest code (you've just made it
different to the remote). You don't need the changes in the remote you want the
remote to have your new history.

In this case, you want to force push your changes so that the remote history
gets re-written with your local one.

```bash
# Force push your branch if your history has changed
git push origin mybranch --force
```

Now you can log into GitLab and create a merge request to get the changes in
your feature branch merged into master.

You can make new local changes and push again, GitLab will automatically update
the merge request.

### Pushing code for Gerrit code review

With Gerrit you don't push your local branch to a remote branch of the same
name, you push it to a special location called _refs/for/master_. This tells
Gerrit that you want to merge your code into master and it sets up a code
review.

Generally, you would have a second remote called _gerrit_ which points to the
repository on the Gerrit server, which is where you push to.

```bash
# Add a remote called gerrit that points to the Git repo on Gerrit
git remote add gerrit user@gerrit:/path/to/code

# Push your code to special location in Gerrit
git push gerrit mybranch:refs/for/master
```

#### Using Git review with Gerrit

The OpenStack project provides a tool called
[git-review](https://docs.openstack.org/infra/git-review/index.html) to make
working with Gerrit easier.

Any code base which is configured to use Gerrit will contain a _.gitreview_ file
in its Git repository that points to the Gerrit server.

In this instance you can simply run _review_ setup to add your remote. This
will verify that you can talk to the server and create your new _gerrit_
remote.

```bash
git review -s
```

You don't manually push your changes, you run the _review_ command from your
branch which will handle all of that for you.

```bash
git review
```

You can make new local commits as required and then re-run the same command
which will update the code review in Gerrit.

## Starting on the next branch
You don't need to wait for your code to merge before working on a new branch,
we can always pull in the latest changes.

Make sure that your local master branch has the latest code before creating
your new branch.

By using the fast-forward merge option, we can make sure we do not have any
local changes in our master branch. If we do have local changes, the pull will
fail. This helps to avoid a situation where you create a branch which has
commits that are not already in upstream master.

```bash
# Switch back to master branch and pull lastest code
# Note: This is a fetch and fast-forward only merge
git checkout master
git pull origin master --ff-only
```

### Reset your master branch

If you happen to have some local commits in master that are not upstream (they
should be in a branch instead) you can get the latest changes and reset your
local master branch to be the same as the remote.

```bash
git checkout master
git remote update
git reset --hard origin/master
```

Now you can create your new branch!

```bash
# Create your new branch and start again...
git checkout -b another-branch origin/master
```

Now continue with making your changes in your new branch.

# Common issues

See the [help page](/help.md) page for some common issues you might encounter,
along with some advice to help you.
