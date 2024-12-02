# ZFS

## ZFS send-receive

```bash
# Need to allow changing properties for send and receive
zfs allow -u <username> create,destroy,mount,diff,clone,hold,snapshot,send,receive,rollback,compression,mountpoint red7

# mount/unmount not really handled by linux
# needs sudo/root

# Initial send
# zfs destroy -fr node8/backups/mlmodels1
zfs send -vR red7/mlmodels1@autosnap_2023-02-18_00:30:03_monthly | ssh nodebox.storage.ioncollector.com zfs receive -vu node8/backups/mlmodels1

# Incremental snapshot send
# If destination is mounted it'll be modified due to (at least) atime changes
# Unmount and use zfs rollback to remove changes
zfs send -vR -i red7/mldata1@autosnap_2023-02-18_00:30:01_monthly red7/mldata1@autosnap_2023-03-01_00:00:09_monthly | ssh nodebox.storage.ioncollector.com zfs receive -vu node8/backups/mldata1

# Keep destination datasets unmounted
zfs set canmount=off node8/backups/mldata1
```
