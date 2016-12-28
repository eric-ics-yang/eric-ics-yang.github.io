###Install X Windows

After install CentOS 7.0 from ISO, it show only a shell login command line.I want to use a X Windows to do some graphic work.

- Use ISO as a repo source.

Setup a repo using ISO.

    mkdir /etc/yum.repos.d/bak
    cd /etc/yum.repos.d/ && mv *.repo bak/
    mount /dev/sr0 /media
    cp bak/CentOS-Media.repo ./
    #Edit CentOS-Media.repo
  ```
  [c7-media]
  name=C7
  baseurl=file:///media/
  gpgcheck=0
  enabled=1
  gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
  ```
      rpm -ivh /media/Packages/yum-utils*.rpm /media/Packages/python-kitchen*.rpm /media/Packages/python-chardet*.rpm
      yum-config-manager --enable c7-media
- Install X Windows

    yum grouplist
    yum groupinstall "X Window System"
  
- Install GNOME or KDE

    yum groupinstall 'GNOME Desktop'
  
- Reboot

- startx

###Debug

After install X Windows without install GNOME, running startx will show error like :
```
xauth file .serverauth does not exist
```
