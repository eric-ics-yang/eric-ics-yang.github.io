### Resume a download with git

Usually we need to download a git repo from github.com or other server, but network connection always is slow. Sometimes, a connection may bread down. If we want to continue git from last breakpoint, we need to do as follows:

```
  mkdir targetrepo
  cd targetrepo
  git init
  git fetch github.com/xxxx.git
  ...  
  From github.com/...
  *branch           HEAD                -> FETCH_HEAD
  
```

FETCH_HEAD will be the git branch of remote repo. You can checkout it now.
