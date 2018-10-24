```shell
# get source file
wget http://openvswitch.org/releases/openvswitch-2.5.6.tar.gz

yum install autoconf automake  libtool rpm-build

./boot.sh ./configure make dist

yum install openssl-devel python-twisted-core python-zope-interface PyQt4 desktop-file-utils groff graphviz selinux-policy-devel libcap-ng-devel

cp openvswitch-2.5.6.tar.gz  ~/rpmbuild/SOURCES/
rpmbuild  -bb rhel/openvswitch-fedora.spec
rpm  -ivh ~/rpmbuild/RPMS/x86_64/openvswitch-2.5.6-1.el7.centos.x86_64.rpm
```
