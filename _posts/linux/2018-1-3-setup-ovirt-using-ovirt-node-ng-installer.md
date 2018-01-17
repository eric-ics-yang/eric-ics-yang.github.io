### Setup ovirt with ovirt-node-ng-installer-x.iso

After install oVirt 4.2.0.2 using ovirt-node-ng-installer-x.iso. We can access at port 9090 on a web admin page which we can deploy ovirt through button on UI.

If you fresh the page, setup process will be not visible.
```
# a process is running on host
root     10413  8470 17 02:59 ?        00:02:25 /bin/python /usr/lib/python2.7/site-packages/otopi/__main__.py  APPEND:BASE/pluginPath=str:/usr/share/ovirt-hosted-engine-setup/scripts/../plugins APPEND:BASE/pluginGroups=str:gr-he-common:gr-he-setup  APPEND:CORE/configFileAppend=str:/tmp/he-setup-answerfile.conf "DIALOG/dialect=str:machine"

```
#### An error like "hosted_storage has been used" may be seen.
Logs are in "/var/log/ovirt-hosted-engine-setup/ovirt-hosted-engine-setup-xxx.log", after investigating into logs, a conf file named
like "answers-20180104062406.conf" is used for setup.
```
OVEHOSTED_ENGINE/enableHcGlusterService=none:None OVEHOSTED_STORAGE/storageDomainName=str:hosted_storage
# update hosted_storage to hosted_storage2 to continue setup.
```
For this environment, a FC storage is been used for hosted_storage(which is not available for hosted-engine setup).
So i setup a nfs storage for hosted_engine deploy.

#### setup through command line

```
hosted-engine --deploy --config-append=/root/answers-20180104062406.conf 
# type in your choice for questions.
```
In the process of setup, it will install a rpm(about 900MB) from web, after installed, a ova file is found at "/usr/share/ovirt-engine-appliance/ovirt-engine-appliance-4.2-20171219.1.el7.centos.ova".
If you have installed that package, you can skip that step.

#### setup a FQDN for setup

During deployment, a FQDN is asked for setup.If there is no DNS service. You can add a line like "192.168.1.1 ovirt-engine.localdomain" to /etc/hosts.
"192.168.1.1" should be a IP can ping from host(after deploy, you can update IP to the IP you want).

#### if deployment is not success, hosted-engine VM will be deleted

You can stop the "hosted-engine" command, and do some fix, then resume "hosted-engine" command.

#### NFS tips
```
#check nfs been mounted
showmount -e 192.168.20.220
Export list for 192.168.20.220:
/data/ovirt/iso    *

```
#### some commands for debug

```
wget --no-check-certificate https://ovirt-engine.localdomain/ovirt-engine/webadmin

vdsm-client Host getStorageDomains

ovirt-hosted-engine-cleanup 

vdsm-client StorageDomain getInfo storagedomainID=9e106f82-6894-46df-886f-167a9d50078f

hosted-engine --add-console-password

# libvirt password
cat /etc/ovirt-hosted-engine/virsh_auth.conf 
[credentials-vdsm]
authname=vdsm@ovirt
password=shibboleth

[auth-libvirt-localhost]
credentials=vdsm


sysctl kern.maxproc
kern.maxproc: 1044 
sysctl  -n kern.maxproc
1044

```

#### FQDN
FQDN是Fully Qualified Domain Name
修改/etc/hosts即可
通过hostname -F /etc/hostname更新主机名。通过hostname -f看到FQDN：

[root@localhost tls]# hostname -F /etc/hostname
[root@localhost tls]# hostname -f

#### vnc password for hosted-engine vm
```
# log info during hosted-engine deployment.
[ INFO  ] Creating VM
          You can now connect to the VM with the following command:
                hosted-engine --console
          You can also graphically connect to the VM from your system with the following command:
                remote-viewer vnc://ovirt4:5900
          Use temporary password "9075mOWH" to connect to vnc console.
          Please ensure that your Guest OS is properly configured to support serial console according to your distro 

documentation.
          Follow http://www.ovirt.org/Serial_Console_Setup#I_need_to_access_the_console_the_old_way for more info.
          If you need to reboot the VM you will need to start it manually using the command:
          hosted-engine --vm-start
          You can then set a temporary password using the command:
          hosted-engine --add-console-password
[ INFO  ] Running engine-setup on the appliance

```

#### The redirection URI for client is not registered
URL: https://localhost:9000/ovirt-engine

This displays this error:

" The redirection URI for client is not registered "

Workaround (posted by Ravi on ovirt devel list)

Create a new conf file /etc/ovirt-engine/engine.conf.d/99-sso.conf and add:
SSO_CALLBACK_PREFIX_CHECK=false

then
systemctl restart ovirt-engine

This will turn off the additional security check for the callback prefix.

https://bugzilla.redhat.com/show_bug.cgi?id=1483544

#### hosts on Windows
像linux的/etc/hosts一样，windows也有一个类似的hosts文件

C:/WINDOWS/system32/drivers/etc/hosts

在这里可以设置本地域名

本地域名是比远程DNS优先级要高的.

