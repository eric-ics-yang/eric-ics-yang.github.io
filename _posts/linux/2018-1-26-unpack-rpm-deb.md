### unpack rpm package

```
rpm2cpio xxx.rpm | cpio -dim
```

### unpack deb and pack deb

The primary command to manipulate deb packages is dpkg-deb. From [easily unpack DEB, edit postinst, and repack DEB]

To unpack the package, create an empty directory and switch to it, then run dpkg-deb to extract its control information and the package files. 
Use dpkg-deb -b to rebuild the package.
```
mkdir tmp
dpkg-deb -R original.deb tmp
# edit DEBIAN/postinst
dpkg-deb -b tmp fixed.deb
```
Beware that unless your script is running as root, the files' permissions and ownership will be corrupted at the extraction stage.
One way to avoid this is to run your script under fakeroot. 
Note that you need to run the whole sequence under fakeroot, not each dpkg-deb individually, since it's the fakeroot process that keeps the memory of the permissions of the files that can't be created as they are.
```
fakeroot sh -c '
  mkdir tmp
  dpkg-deb -R original.deb tmp
  # edit DEBIAN/postinst
  dpkg-deb -b tmp fixed.deb
'
```
Rather than mess with permissions, you can keep the data archive intact and modify only the control archive. 
dpkg-deb doesn't provide a way to do that. Fortunately, deb packges are in a standard format: they're ar archives. 
So you can use ar to extract the control archive, modify its files, and use ar again to replace the control archive by a new version.
```
mkdir tmp
cd tmp
ar p ../original.deb control.tar.gz | tar -xz
# edit postinst
cp ../original.deb ../fixed.deb
tar czf control.tar.gz *[!z]
ar r ../fixed.deb control.tar.gz
```
You should add a changelog entry and change the version number if you modify anything in the package. 
The infrastructure to manipulate Debian packages assumes that if two packages have the same name and version, they're the same package. 
Add a suffix to the debian_revision part at the end of the version number; for sorting reasons the suffix should start with ~, e.g. 1.2.3-4.1 becomes 1.2.3-4.1~johnjumper1.

Instead of using shell tools, you can use Emacs. 
The dpkg-dev-el package (which is its own upstream as this is a native Debian package) contains modes to edit .deb files and to edit Debian changelogs. 
Emacs can be used interactively or scripted.

  [easily unpack DEB, edit postinst, and repack DEB]:https://unix.stackexchange.com/questions/138188/easily-unpack-deb-edit-postinst-and-repack-deb
