###Download env setting
```sh
fuel env --list
fuel --env ID settings --download
```

###Download task graph and render it to png
tips from [fuel-docs]
```sh
yum install graphviz
rpm -ivh python-pygraphviz-1.3-2.rc2.fc22.x86_64.rpm (or rpm -i  https://www.dropbox.com/s/1fyv9p55kslbxkg/python-pygraphviz-1.3-3.rc2.el7-mos1.x86_64.rpm)
fuel graph --env ID --download > env_ID.gv
fuel graph --render env_ID.gv --tred &

```

  [fuel-docs]:http://docs.openstack.org/developer/fuel-docs/plugindocs/fuel-plugin-sdk-guide/create-plugin/actions-existing-roles/deployment-stages.html
