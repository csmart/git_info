# Git internals

Git is essentially just a file system that lets you read and write content.

Under the hood, it is a key-value store database. You put information in
(value) and you get a commit hash back (the key). Using the key you can
retrieve the information again in the future.

Git stores different types of objects, which together make up your repository.

* blob - the content of the commit, file name is a hash of the content
* tree - directory information, each file links to a blob or other tree hash
* commit - commit details such as message and author, links to other commits and
  tree objects together to form history
* tag - points to a commit object and stores additional metadata like
  signatures

Unlike subversion where each commit gets an incremented number, each commit in
Git has a unique hash which is made from the information above. Therefore it
provides cryptographic authentication of the history so it is not possible to
change it at any point without it being noticed.

To reference a commit, you use that hash.

The content and history of the repository at any point in time is made possible
by linking all the commits together in a sort of [Merkle
tree](https://en.wikipedia.org/wiki/Merkle_tree).

Git also has an index for the staging area (where you prepare commits) and
refs (references). The refs objects point to commits and are used for things
like branches and remotes as well as the workspace (HEAD). So a branch is
really just a pointer to a commit.

