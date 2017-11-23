### kernel: CIFS VFS: cifs_mount failed w/return code=-121

After mount a shared dir of Windows OS to a linux server, we can access files in that dir.
We find some error code when mounting a dir.
```
kernel: CIFS VFS: cifs_mount failed w/return code=-121
#logs on cmd: failed: Remote I/O error. Internel general exception information
#Maybe also because of Firewall is open, or not shared on network.

kernel: CIFS VFS: Error connecting to socket. Aborting operation.
kernel: CIFS VFS: cifs_mount failed w/return code=-115
#logs of commandline: failed: Operation now in progress. Internel general exception information
#This is because of Firewall is open.
```
