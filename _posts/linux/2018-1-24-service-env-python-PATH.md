### Which version of python is used when python 2.6 and 2.7 is installed?

On ubuntu14 server, default python version is 2.7.6, when you install a python 2.6 through source(make install) or deb, you will
find "python --version" changed(maybe after reboot).

If you type in "which python", output will be "/usr/local/bin/python". So what's problem? What configuration affects default python?

If you want to change python to default in current session, you can type "alias python=/usr/bin/python", if you want to change defaltu python 
after a user login, you can put that command in that user's "~/.bashrc".

I check environment with "env" command, i found PATH is something like "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin".
So the problem is PATH find python in /usr/local/xxx first before check /usr/bin/python.

### How to change python used in service?

But the problem i am facing is: a program is been started by "service xxx start", what affects the python been used by service?
Two files are found, "/etc/init.d/xxx"  and "/etc/init/xxx.conf"， i changed first file, but nothing changed. After many times retry,
i found send file is the right file, it contains "python xxx.py" command which start the program.

So the fix is change that commnad to "/usr/bin/python xxx.py".

### Customize env in a shell

"/etc/bashrc", "/etc/profile", "/etc/environment", "/etc/bash.bashrc" affect global env of shell.
"echo $HOME" to show a user's home dir. "env" and "set" to show local environment variables."readonly TEMP_ENV" can set a variable readonly.

### virtualenv

[virtualenv]

```
pip install virtualenv

```

  [virtualenv]:https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001432712108300322c61f256c74803b43bfd65c6f8d0d0000
