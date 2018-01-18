### Build wheel

安装python-setuptools, python-wheel之后，执行： 

    python setup.py bdist_wheel 

就可以打包wheel包了。打包之后的文件在 dist目录下。

### Build egg

Build an egg with .pyc only(without .py file).
```
python setup.py bdist_egg --exclude-source-files

```

### Build rpm

```
python setup.py bdist_rpm
python setup.py bdist_rpm --help
python setup.py --help-commands
python setup.py bdist_rpm --install-script install.script --post-install post.sh --pre-uninstall preun.script --post-uninstall postun.sh --requires gcc,automake --binary-only

```

If you want to build a rpm package with setuptools which contains only .pyc .pyo file but .py file. But bdist_rpm has no options like "--exclude-source-files" Here is a trick.
```
#install.script
python setup.py install --single-version-externally-managed -O1 --root=$RPM_BUILD_ROOT --record=INSTALLED_FILES
sed -i "/.py$/d" INSTALLED_FILES
%define _unpackaged_files_terminate_build 0
```
[Handling error for unpackaged files]
Starting with RPM 4.1, rpmbuild will exit if all files in the $RPM_BUILD_ROOT directory are not found in the %files section (or in a file that lists file names used with the -f option). This is officially known as a Fascist build policy and you can turn it off with the following macros.
The %_unpackaged_files_terminate_build macro, if set to 1, tells rpmbuild to exit if it finds files that are in the $RPM_BUILD_ROOT directory but not listed as part of the package. Set this macro to 0 to turn off the Fascist build policy. For example:
%define _unpackaged_files_terminate_build 0
You can also control the flag that specifies whether missing documentation files cause rpmbuild to exit. Set the %_missing_doc_files_terminate_build macro to 0 to turn off this feature:
%define _missing_doc_files_terminate_build 0
See the "Defining Spec File Macros" section later in the chapter for more on using macros.

  [Handling error for unpackaged files]:https://docs-old.fedoraproject.org/ro/Fedora_Draft_Documentation/0.1/html/RPM_Guide/ch09s05s07.html


### MANIFEST.in
Some example about include/exclude files:
```
prune tests/
include LICENSE  README.md RELEASE
#if you have static files to be added, add them with
...
recursive-include package1/static *

```
