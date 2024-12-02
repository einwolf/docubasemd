# Fedora

Add a kernel parameter

`/etc/default/grub` will apply when kernel is updated by dnf.

```bash
grubby --update-kernel=ALL --args="mitigations=off"
```

Rebuild initramfs

```bash
dracut --force --regenerate-all
```

Make samba shares show up in windows networking

```bash
# Install Web Services Dynamic Discovery
dnf install -y wsdd
systemctl enable --now wsdd
```
