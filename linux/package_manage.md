### yum

On CentOS yum is a import package management software. After install some packages with yum, if you want to undo that, instead of remove packages intalled, we can use command:

```shell
yum history
yum history info N  #show informaction of an install
yum history undo N  #undo an install
yum history redo N  #redo an install
```
