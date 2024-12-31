# ZFS

## ZFS send-receive

```bash
# Need to allow changing properties for send and receive
zfs allow -u <username> create,destroy,mount,diff,clone,hold,snapshot,send,receive,rollback,compression,mountpoint red7

# mount/unmount permission not handled by zfs in linux
# Needs sudo/root to mount/unmount

# Initial send
# zfs destroy -fr node8/backups/mlmodels1
# -R will mirror snapshot deletions
zfs send -vR red7/mlmodels1@autosnap_2023-02-18_00:30:03_monthly | ssh storage-host zfs receive -vu node8/backups/mlmodels1

# Incremental snapshot send
# If destination is mounted it'll be modified due to (at least) atime changes.
# This prevents the incremental send from working.
# Unmount and use zfs rollback to remove changes.
zfs send -vR -i red7/mldata1@autosnap_2023-02-18_00:30:01_monthly red7/mldata1@autosnap_2023-03-01_00:00:09_monthly | ssh storage-host zfs receive -vu node8/backups/mldata1

# Keep destination datasets unmounted
zfs set canmount=off node8/backups/mldata1

# Use zfs rollback to "choose" the snapshot for the zfs file system
zfs rollback red7/mldata1@autosnap_2023-02-18_00:30:01_monthly
```

```bash
# pull vs push backup
 host1# zfs send -vR tank/foo@snapshot | ssh host2 zfs receive -vu othertank/foo
 host2# ssh host1 zfs send -vR tank/foo@snapshot | zfs receive -vu othertank/foo
 ```

## Syncoid

```bash
# Pull backup
# --no-privilege-elevation to use zfs command permissions
syncoid --recursive storage-host:node8/dataset backuphost/dataset
```
