### Topic on gerrit
opic是一个把一组changes归集到某个分类里的方法。

比如有一个很大的feature需要由若干个人一起来完成。
每个人都在自己的change里面工作。那么就可以把这一组changes全部都通过一个topic归集到一起。

无论是draft或者publish状态的change都可以添加topic。

```
git review -t topic_test
```

### Draft on gerrit
Draft状态下的change只有提交者自己能看到。处于draft状态的change一样可以添加reviewer。

被添加的reviewer可以看到这个change。

Draft可以方便开发者在Gerrit里保存一些需要较长时间完成的工作。或者是一些实现方法需要小范围讨论的feature。
当处于draft状态的某个change修改的差不多可以公开review的时候，可以在Web界面上点publish按钮
```
git review -D 
git review --draft
```
