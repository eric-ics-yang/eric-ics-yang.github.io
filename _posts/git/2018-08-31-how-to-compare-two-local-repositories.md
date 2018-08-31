### How to compare two local repositories

In Git you can use, being in one of repositories

>GIT_ALTERNATE_OBJECT_DIRECTORIES=../repo/.git/objects git-diff-tree $(GIT_DIR=../repo/.git git rev-parse --verify HEAD) HEAD

if you want to compare current repository with ../repo/ repository.

In the obsoleted Cogito you would nearly exactly the same

>GIT_ALTERNATE_OBJECT_DIRECTORIES=../repo/.git/objects cg-diff -r `GIT_DIR=../repo/.git cg-object-id -c HEAD`..HEAD


>GIT_ALTERNATE_OBJECT_DIRECTORIES=../kubernetes.orgin/.git/objects git diff-tree --patch $(GIT_DIR=../kubernetes.orgin/.git git rev-parse --verify HEAD) HEAD

From [tips].
  [tips]:https://git.wiki.kernel.org/index.php/GitTips
