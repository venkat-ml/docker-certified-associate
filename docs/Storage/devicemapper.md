The devicemapper driver uses block devices dedicated to Docker and operates at the block level, rather than the file level.

With Docker 17.06 and higher, Docker can manage the block device for you, simplifying configuration of direct-lvm mode. This is appropriate for fresh Docker setups only.

You can only use a single block device. If you need to use multiple block devices, configure direct-lvm mode manually instead.


Device Mapper is a kernel-based framework that underpins many advanced volume management technologies on Linux.

Docker’s devicemapper storage driver leverages the thin provisioning and snapshotting capabilities of this Device Mapper framework for image and container management.

Particular storage-driver can be configured with options specified with --storage-opt flags. Options for devicemapper are prefixed with dm.

Device Mapper configuration modes:

- **loop-lvm mode** - is only appropriate for testing. The loop-lvm mode makes use of a ‘loopback’ mechanism that allows files on the local disk to be read from and written to as if they were an actual physical disk or block device.
- **direct-lvm mode** - Production hosts using the devicemapper storage driver must use direct-lvm mode. This is faster than using loopback devices, uses system resources more efficiently, and block devices can grow as needed. However, more setup is required than in loop-lvm mode.

- The devicemapper driver uses block devices dedicated to Docker and operates at the block level, rather than the file level.

- With Docker 17.06 and higher, Docker can manage the block device for you, simplifying configuration of direct-lvm mode. This is appropriate for fresh Docker setups only.

- You can only use a single block device. If you need to use multiple block devices, configure direct-lvm mode manually instead.

- The vfs storage driver is intended for testing purposes, and for situations where no copy-on-write filesystem can be used. Performance of this storage driver is poor, and is not generally recommended for production use.

- devicemapper, btrfs, and zfs are block-level storage drivers.

- The overlay storage driver only works with 2 layers: lowerdir and upperdir.