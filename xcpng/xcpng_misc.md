# Xcp-ng misc

```bash
# xcp-ng 8.3 maximum disk size 1.9 TB
2088960 MiB

# Maximum of 7 ethernet devices per VM
```

## VM

```bash
# Update / recreate VM templates
/usr/bin/create-guest-templates

# Import xva from CLI
# Gives no feedback. See xe task-list.
# In xcp-ng there isn't a way to import ova from cli.
# preserve is keep mac address or not
xe vm-import host-username=<user> host-password=<pw> preserve=false sr-uuid=<uuid> filename=<file.xva>
```

## dracut initramfs

```bash
# Linux driver name for Xen disks is xen-blkfront
dracut -f -v --add-drivers "xen-blkfront xen-netfromt" --regenerate-all
```
