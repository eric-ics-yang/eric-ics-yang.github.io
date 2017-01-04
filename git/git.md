###Add tag

Add a tag with signature, and push tag to origin.

    git tag -s -u YOURNAME -m "COMMENT FOR TAG"  x.y.z  xxxxxcommitid
    git push origin --tags


###git-dir & work-tree

git-dir(.git dir) and work-tree is the same by default, sometime are not. We can define them with cmd switch.
```
git --git-dir=./.git --work-tree=../newtree_for_some_commit status
# Lots of file will be seen lost in newtree_for_some_commit dir
git --git-dir=./.git --work-tree=../newtree_for_some_commit checkout -b tmp -f master
# force checkout master to newtree_some_commit
git --git-dir=./.git --work-tree=../newtree_for_some_commit branch -d tmp

```
