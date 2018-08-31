### How to compare two local repositories

In Git you can use, being in one of repositories

>GIT_ALTERNATE_OBJECT_DIRECTORIES=../repo/.git/objects git-diff-tree $(GIT_DIR=../repo/.git git rev-parse --verify HEAD) HEAD

if you want to compare current repository with ../repo/ repository.

In the obsoleted Cogito you would nearly exactly the same

>GIT_ALTERNATE_OBJECT_DIRECTORIES=../repo/.git/objects cg-diff -r `GIT_DIR=../repo/.git cg-object-id -c HEAD`..HEAD


>GIT_ALTERNATE_OBJECT_DIRECTORIES=../kubernetes.orgin/.git/objects git diff-tree --patch $(GIT_DIR=../kubernetes.orgin/.git git rev-parse --verify HEAD) HEAD

From [tips](https://git.wiki.kernel.org/index.php/GitTips).


### How do I compare two git repositories?

- Solution  1

Since the two git repositories start out the same, you can pull both into the same working repository.
```
$ git remote add cvsimport-a git://.../cvsimport-a.git
$ git remote add cvsimport-b git://.../cvsimport-b.git
$ git remote update
$ git log cvsimport-a/master..cvsimport-b/master  # in B, not in A?
$ git log cvsimport-b/master..cvsimport-a/master  # in A, not in B?
```
- Solution 2

In one of your repos, do the following:
```
$ git remote add other-repo git://.../other-repo.git
$ git remote update
$ git log other-repo/master...master
  # or maybe:
$ gitk master... other-repo/master
```
This gives you the symmetric difference between two repos; this shows you all of the commits that are in one repo, but not the other.


From [stackoverflow](https://stackoverflow.com/questions/687450/how-do-i-compare-two-git-repositories)
