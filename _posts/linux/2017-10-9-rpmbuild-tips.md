### Sign rpm pkgs
#### Create gpg keys

Use gpg command to create a gpg key for you pkg signature.

    mkdir ~/.gnupg (maybe it's optional)
    gpg --gen-key
    gpg --list-keys

#### Config your RPM macros

Edit ~/.rpmmacros, add your gpg key configuration.

    %_signature gpg
    %_gpg_path PATH_TO_.gnupg
    %_gpg_name YOUR NAME
    %_gpgbin /usr/bin/gpg


#### Build pkgs with sign

build pkgs

    rpmbuild -v -ba --sign --clean SPECS/your.spec

#### Export gpg public key

Export your gpg public key so that target OS can import it to verfy pkgs published by you.

    gpg --armor --output YOUR-GPG-KEY --export 'YOUR NAME'

#### Import Your gpg key

Import gpg key to verfy installed pkgs.

    rpm --import YOUR-GPG-KEY

#### Refs

References:[1],   [2]

  [1]: http://www.rpm.org/max-rpm/ch-rpm-pgp.html
  [2]: https://www.gnupg.org/gph/en/manual.html


### rpmbuild macros

#### Macros

[Macros] are text in the format %{string}.

```
Typical macros:
Macro 	Typical Expansion 	Meaning
%{_bindir} 	/usr/bin 	Binary directory: where executables are usually stored.
%{_builddir} 	~/rpmbuild/BUILD 	Build directory: files are compiled within a subdirectory of the build directory. See %buildsubdir.
%{buildroot} 	~/rpmbuild/BUILDROOT/%{name}... 	Build root: where files are "installed" during the %install stage, which copies files from a subdirectory of %{_builddir} to a subdirectory of %{buildroot}. (Historically, %{buildroot} was in "/var/tmp/".)
%{buildsubdir} 	%{_builddir}/%{name} 	Build subdirectory: a subdirectory within %{_builddir} where files are compiled during the %build stage. It is set after %autosetup.
%{_datadir} 	/usr/share 	Share directory.
%{_defaultdocdir} 	/usr/share/doc 	Default documentation directory.
%{dist} 	.fcNUMBER 	Distribution+version short name (e.g. ".fc27")
%{fedora} 	NUMBER 	Number of fedora release (e.g. "27")
%{_includedir} 	/usr/include
%{_infodir} 	/usr/share/info
%{_initrddir} 	/etc/rc.d/init.d
%{_libdir} 	/usr/lib
%{_libexecdir} 	/usr/libexec
%{_localstatedir} 	/var
%{_mandir} 	/usr/share/man
%{name} 		Name of package, set by Name: tag
%{_sbindir} 	/usr/sbin
%{_sharedstatedir} 	/var/lib
%{_sysconfdir} 	/etc
%{version} 		Version of package, set by Version: tag

Learn more about macros by looking in /etc/rpm/* and /usr/lib/rpm, especially /usr/lib/rpm/macros. Also use rpm --showrc to show values that RPM will use for macros (altered by rpmrc and macro configuration files).

You can set your own macro values using %global, but be sure to define them before you use them. (Macro definitions can also refer to other macros.) For example:

%global date 2012-02-08

Use the "-E" option of rpmbuild to find the value of a macro in a SPEC file:

rpmbuild -E '%{_bindir}' myfile.spec
```

  [Macros]:http://fedoraproject.org/wiki/How_to_create_an_RPM_package
