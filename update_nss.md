###Problem: Update a nss installation with rpm

After **yum update** from CentOS 7.1 to 7.2. nss package version is **nss-3.19.1-19.el7_2.x86_64.rpm**.
I want to install libvirt, but **yum install libvirt** says:
```
Error: Protected multilib versions: nss-3.19.1-18.el7.x86_64 != nss-3.19.1-19.el7_2.x86_64
Error: Protected multilib versions: numactl ...
Error: Protected multilib versions: gnutls ...
...

```
A lot of pakcage's version is wrong.

###Solution:
Need to remember nss package affects command **rpm, yum, ssh, scp **!
After delete a nss package, command **rpm, yum, ssh, scp** all become unuseful.
You need to extract libnss3.so libnssutil3.so from nss-3.19.1-18.el7.x86_64.rpm nss-util-3.19.1-4.el7_1.x86_64.rpm before delete nss and nss-utils package.
```sh
rpm2cpio nss-3.19.1-18.el7.x86_64.rpm nss-util-3.19.1-4.el7_1.x86_64.rpm | cpio -dim
rpm -e --nodeps nss nss-util nss-tools && rpm -ivh nss-*.rpm
rpm -e --nodeps nss-sysinit && rpm -ivh nss-sysinit-3.19.1-18.el7.x86_64.rpm
yum remove numactl-libs && yum -y install irqbalance
rpm -e --nodeps gnutls gnutls-dane gnutls-utils && yum -y install gnutls gnutls-dane gnutls-utils

yum check dependencies # check package's dependency after you update packages above.
```
