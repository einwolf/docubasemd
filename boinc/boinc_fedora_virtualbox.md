# Boinc with Virtualbox

Install Boinc with virtualbox enabled on Fedora.

```bash
# Install boinc normally
dnf install -y boinc-client

# Install VirtualBox from virtualbox.org
usermod -a -G vboxusers boinc
usermod -a -G vboxusers normaluser

# Install extensions from normaluser

# Selinux prevents boinc-client.service from executing VBoxManage
# Specifically VBoxManage can't use /usr/bin/lsmod
# Set SELINUX=permissive in /etc/selinux/config
```

```bash
# Shows in log
Mar 24 16:15:50 boinc4 boinc[2511]: 24-Mar-2023 16:15:50 [---] OS: Linux Fedora Linux: Fedora Linux 37 (Workstation Edition) [6.2.7-200.fc37.x86_64|libc 2.36]
Mar 24 16:15:50 boinc4 boinc[2511]: 24-Mar-2023 16:15:50 [---] Memory: 7.74 GB physical, 7.74 GB virtual
Mar 24 16:15:50 boinc4 boinc[2511]: 24-Mar-2023 16:15:50 [---] Disk: 195.80 GB total, 185.78 GB free
Mar 24 16:15:50 boinc4 boinc[2511]: 24-Mar-2023 16:15:50 [---] Local time is UTC -4 hours
Mar 24 16:15:50 boinc4 boinc[2511]: 24-Mar-2023 16:15:50 [---] VirtualBox version: 7.0.6r155176
```

```bash
# A systemd service setting is preventing VIrtualBox communication
# Ref https://github.com/BOINC/boinc/issues/3355
# VBoxManage: error: Failed to create the VirtualBox object!
# ProtectSystem=strict is too strict

# Make /etc/systemd/system/boinc-client.service.d/boinc-client.service
[Service]
ProtectSystem=full
```
