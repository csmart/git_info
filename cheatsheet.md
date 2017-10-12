# Git cheatsheet
Below are some common commands you will use with Git. It is by _no means_
exhaustive, but should cover the majority of tasks.

<!-- vim-markdown-toc GFM -->

* [Configuring Git](#configuring-git)
* [Getting a repository](#getting-a-repository)
* [Workspace](#workspace)
* [Working with branches](#working-with-branches)
* [Working with the code](#working-with-the-code)
* [Sharing and getting code](#sharing-and-getting-code)

<!-- vim-markdown-toc -->

# Configuring Git

| Command | Purpose |
| --- | --- |
| git config --global user.name &lt;your name&gt; | Default name to use in commits |
| git config --global user.email &lt;your email&gt; | Default email to use in commits |
| git config --global core.editor /usr/bin/vim | Set vim as default editor |

# Getting a repository

| Command | Purpose |
| --- | --- |
| git init &lt;dir&gt; | Initialise a repository at &lt;dir&gt; |
| git clone &lt;url&gt; &lt;dir&gt; | Clone a remote repository to &lt;dir&gt; |

# Workspace

| Command | Purpose |
| --- | --- |
| git status | Show status of local workspace |
| git diff | Show changes to tracked files |
| git diff --cached | Show changes to added files in staging area |

# Working with branches

| Command | Purpose |
| --- | --- |
| git branch --all | List all local and remote branches |
| git branch &lt;new branch&gt; &lt;old branch&gt; | Create a new branch |
| git checkout &lt;branch&gt; | Switch workspace to a branch |
| git checkout -b &lt;new branch&gt; &lt;old branch&gt; | Create and switch to new branch |

# Working with the code

| Command | Purpose |
| --- | --- |
| git add &lt;file(s)&gt; | Stage changes in file for commit |
| git reset &lt;file(s)&gt; | Remove file from staging area but keep changes in workspace |
| git reset --hard | Revert workspace to state at latest commit, losing all changes |
| git checkout &lt;file(s)&gt; | Checkout latest committed copy of a file |
| git commit &lt;file(s)&gt; | Commit changes in files from staging area |
| git commit --all &lt;file(s)&gt; | Commit all changes in all tracked files |

# Sharing and getting code

| Command | Purpose |
| --- | --- |
| git push &lt;remote&gt; &lt;branch&gt; [--force] | Share your code, optional force |
| git fetch --all | Fetch latest changes from all remotes |
| git merge &lt;branch&#124;commit&#124;ref&gt; | Merge changes into branch in workspace |
| git pull &lt;remote&gt; &lt;branch&gt; | Fetch & merge latest changes from remote branch |
| git rebase &lt;branch&#124;commit&#124;ref&gt; | Put local commits on top of latest changes |

