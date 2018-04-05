### Cache of OS

For I/O of process in OS, we make memory as cache of disk. There three type of cache mode:

- Write-through cache 

Write to cache and through to disk simultaneous. 

- Write-around cache 

Write to disk directly by-pass cache(around).

Based on access patterns, a copy of that data may be promoted to cache.


- Write-back cache 

Write to cache, which captures all new or modified data. Those data will be write back to disk asynchronously.

However, the write acknowledgement to the application comes after data is written to flash, not to the hard disk,
meaning that writes now occur at flash speeds. 

reference [1] [2]

  [1]:http://searchstorage.techtarget.com/answer/Comparing-write-through-write-back-and-write-around-caching
  [2]:http://www.computerweekly.com/feature/Write-through-write-around-write-back-Cache-explained


### Cache of hypervisor

- Cache=none

Host page cache is disabled.

But may be kept in a writeback disk cache. Use this option for guests with large I/O requirements.[3] 

This option is generally the best choice, and is the only option to support migration.[3]

- Cache=writethrough

Host page cache is enabled, but I/O is written through to the physical medium.[3]
Suggested for guests that do not support a writeback cache (such as Red Hat Enterprise Linux 5.5 and earlier), where migration is not needed.[3]

- Cache=writeback

Host page is enabled.
This mode causes the hypervisor to interact with the disk image file or block device with neither O_DSYNC nor O_DIRECT semantics, so the host page cache is used and writes are reported to the guest as completed when they are placed in the host page cache. [4]

- Cache=directsync
	
Similar to writethrough, but I/O from the guest bypasses the host page cache.[3]
Like writethrough, it is helpful to guests that do not send flushes when needed.[4]

- Cache=unsafe

The host may cache all disk I/O, and sync requests from guest are ignored.[3]

reference [3], [4], [5]([5]may be not found ).

  [3]: https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/virtualization_tuning_and_optimization_guide/sect-virtualization_tuning_optimization_guide-blockio-caching
  [4]: https://doc.opensuse.org/documentation/leap/virtualization/html/book.virt/cha.cachemodes.html
  [5]: https://www.ibm.com/support/knowledgecenter/linuxonibm/liaat/liaatbpkvmguestcache.htm  

