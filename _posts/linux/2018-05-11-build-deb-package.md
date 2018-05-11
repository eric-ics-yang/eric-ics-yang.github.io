### rules

This is he exact rules(debian/rules) that dpkg-buildpackage will use to actually create the package. 

This file is in fact another Makefile, but different from the one(s) in the upstream source. 

Unlike other files in debian, this one is marked as executable.

Newer dh_make generates a very simple but powerful default rules file using the dh command:
```
 1 #!/usr/bin/make -f
 2 # See debhelper(7) (uncomment to enable)
 3 # output every command that modifies files on the build system.
 4 #DH_VERBOSE = 1
 5 
 6 # see EXAMPLES in dpkg-buildflags(1) and read /usr/share/dpkg/*
 7 DPKG_EXPORT_BUILDFLAGS = 1
 8 include /usr/share/dpkg/default.mk
 9 
10 # see FEATURE AREAS in dpkg-buildflags(1)
11 #export DEB_BUILD_MAINT_OPTIONS = hardening=+all
12
13 # see ENVIRONMENT in dpkg-buildflags(1)
14 # package maintainers to append CFLAGS
15 #export DEB_CFLAGS_MAINT_APPEND  = -Wall -pedantic
16 # package maintainers to append LDFLAGS
17 #export DEB_LDFLAGS_MAINT_APPEND = -Wl,--as-needed
18 
19 # main packaging script based on dh7 syntax
20 %:
21         dh $@ 
```
(I've added the line numbers and trimmed some comments. In the actual rules file, the leading spaces are a TAB code.)

  [rules]:https://www.debian.org/doc/manuals/maint-guide/dreq.en.html#rules
