###Download devstack

- 下载项目
使用有sudo权限的非root用户（无论时CentOS或Debian系列）clone一份devstack的git库下来。
```
git clone https://git.openstack.org/openstack-dev/devstack
```
- 添加stack用户
```
devstack/tools/create-stack-user.sh
su stack；pwd （用当前用户或root用户）
```
得到stack的home目录
- 拷贝devstack到stack的home目录
```
  cp -R devstack /path/to/stack/home
```
保证devstack的属性 stack:stack


