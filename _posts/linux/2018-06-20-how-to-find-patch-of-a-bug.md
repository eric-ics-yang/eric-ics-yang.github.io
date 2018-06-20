### bug fix advisory
[RHBA] gives a list of bugs been fixed at that time.
  [RHBA]: https://access.redhat.com/errata/RHBA-2016:0362
  
### vmchannel thread consumes 100% of CPU
For example, [vmchannel] consumes 100% of CPU is a bug been fixed, the patch is on the page of bugzilla.

External Trackers

|Tracker|	ID|	Priority|	Status|	Summary|	Last Updated|
|--------|--------|--------|--------|-----|---------|
|oVirt gerrit	|51840|	ovirt-3.6|	MERGED|	virt: Correct epoll unregistration usage in vmchannels|	2016-01-15 08:49 EST|
|oVirt gerrit	|51877|	ovirt-3.6	|MERGED	|virt: Set cloexec flag on channel sockets	2016-01-15 |08:48 EST|

Those tracker is the patches related with this bug. 

We need to find the right "Priority" which related with our branch, and patch all those patches.
  [vmchannel]: https://bugzilla.redhat.com/show_bug.cgi?id=1226911
