# Fedora

Add a kernel parameter

`/etc/default/grub` will apply when kernel is updated by dnf.

```bash
grubby --update-kernel=ALL --args="mitigations=off"
```

Rebuild initramfs

```bash
dracut -v --force --regenerate-all
```

Make samba shares show up in windows networking

```bash
# Install Web Services Dynamic Discovery
dnf install -y wsdd
systemctl enable --now wsdd
```

Update rescue kernel

```bash
# Reinstalls kernel
rm -f /boot/*-rescue-*
kernel-install add $(uname -r) /lib/modules/$(uname -r)/vmlinuz

# Might be better
grubby \
--update-kernel="/boot/vmlinuz-0-rescue-$(cat /etc/machine-id)" \
--remove-args="rhgb quiet" --args="systemd.unit=rescue.target"
```
