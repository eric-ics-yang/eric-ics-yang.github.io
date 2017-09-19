### delete all empty files 
```sh
find . -name "*" -type f -size 0c | xargs -n 1 rm -f
find . -name "*" -type f -size 1024c | xargs -n 1 rm -f //delete 1k files
//different with -size 1k which is disk space used by that file.

```
