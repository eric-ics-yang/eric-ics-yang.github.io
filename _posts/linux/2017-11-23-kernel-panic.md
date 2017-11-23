### update kernel from 2.6.32 to 3.10.33, kernel panic

A project need to update kernel from 2.6.32 to 3.10.33-rt, a real time kernel.
After kernel update, it failed to boot into OS，error message:
```
Kernel panic - not syncing: Attempted to kill init! exitcode=0x00000100
CPU: 5 PID: 1 Comm: init Not taninted 3.10.33-rt32.45.3.ky3rt.x86_x64 #1
......

```

Force reboot, at kernel select menu, edit kernel boot commandline, remove "quiet" argument.
Then boot OS， you can see verbose output log of OS boot process.
Output:
```
draut Warning: No root device "block:/dev/mapper/VolGroup-lv_root" found

draut Warning: LVM VolGroup/lv_swap not found
draut Warning: LVM VolGroup/lv_root not found

draut Warning: Boot has failed. To debug this issue add "rdshell" to the kernel command line
......

draut Warning: Signal caught!
Kernel panic - not syncing: Attempted to kill init! exitcode=0x00000100
CPU: 5 PID: 1 Comm: init Not taninted 3.10.33-rt32.45.3.ky3rt.x86_x64 #1
```

Solution: Try to update disk type from virtio to IDE， solve the problem.

