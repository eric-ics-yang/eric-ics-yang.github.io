### Download devstack

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

配置pip源

向 ~/.pip/pip.conf中写入： 
    [global]
    index-url = http://192.168.1.1/siple
    
    [install]
    trusted-host = 192.168.1.1
    
配置iptables

    iptables -A nova-api-INPUT -p tcp --dport 80 -j ACCEPT
    
### debug tips

- pip version

pip 8.1.2 无法找到内网用tomcat搭建的pypi源中的oslo.concurrency; pip 8.0.2无法找到greenlet； pip 8.1.1可以找到上述两个。

pip的一个缓存位置： /usr/lib/pythin2.7/site-packages/virtualenv_support/pip-8.1.2-py2.py3-none-any.whl

- tox failed because of pip install

在执行tempest测试之前需要配置tox环境，而以下命令执行失败

    tox --notest -efull
    ...failed to find pypi compatible xxx version  
而上述命令在执行pip安装软件之前花费了较长时间，多次调试失败，浪费大量时间。应该直接执行

    .tox/tempest/bin/pip install xxxx
来确认具体错误原因。
