### steps to add a selinux rule
``` shell
cat /var/log/audit/audit.log | audit2allow -M local
Generating type enforcment file: local.te
Compiling policy: checkmodule -M -m -o local.mod local.te
Building package: semodule_package -o local.pp -m local.mod

******************** IMPORTANT ***********************

In order to load this newly created policy package into the kernel,
you are required to execute

semodule -i local.pp
```
