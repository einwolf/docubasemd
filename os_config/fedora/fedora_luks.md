# Fedora setup LUKS

## Extra partition added manually

```bash
cryptsetup luksFormat --type luks2 --sector-size 4096 --force-password /dev/sdb1
cryptsetup open /dev/sdb1 sdb1_encrypted
# use /dev/mapper/sdb1_encrypted
mount  /dev/mapper/sdb1_encrypted /mnt/sdb1
cryptsetup close /dev/mapper/sdb1_encrypted

# Online encrypt
cryptsetup reencrypt --encrypt --init-only --reduce-device-size 32M /dev/sdb1 sdb1_encrypted
```

```bash
# fstab manual mount with systemd

cryptsetup luksUUID /dev/mapper/sdb1_encrypted
# blkid gives the same UUID

# fstab
/dev/mapper/sdb1    /mnt/sdb1    ext4    noauto

# crypttab
# sdb1 here is /dev/mapper/sdb1
sdb1    UUID=""    none    noauto

# manual mount
# asks for password
systemctl start systemd-cryptsetup@sdb1.service
mount /mnt/sdb1
# or
systemctl start mnt-staging.mount
```
