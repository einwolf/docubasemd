# Dell racadm

```bash
/opt/dell/srvadmin/bin/idracadm7 help

# local
/opt/dell/srvadmin/bin/idracadm7 getsensorinfo

# remote
/opt/dell/srvadmin/bin/idracadm7 -i -r 192.168.1.115 getsensorinfo
```

## vflash

I didn't get this to work consistently.
It did not load from samba1 at all. Stays at 30% and times out or goes to 100% and doesn't show partition in vflash.
It would load half the image from strongbox. It would go to 100%.

```bash
/opt/dell/srvadmin/bin/idracadm7 -u root -p calvin -r 192.168.1.115 vflashpartition list
/opt/dell/srvadmin/bin/idracadm7 -u root -p calvin -r 192.168.1.115 vflashpartition status -a

# Needs to use a guest SMB share. Not sure on supported SMB versions.
# Guest share user needed no password set (smbpasswd -n localsysadmin)
# Second -u and -p are user and password for SMB share.
# Don't appear to be able to stop upload.
# vflashpartition status -a shows progress
# Progress starts at 30% and jumps to 100% on success or failure.

# Loads at 4 MB/sec
# Both servers spew NT_ACCESS_DENIED errors from flush requests.
# Image still loads ok.

# Guest share on samba1
# This had problems where it times out and doesn't finish.
/opt/dell/srvadmin/bin/idracadm7 -u root -p calvin -r 192.168.1.115 vflashpartition create -i 1 -o f42b -e cddvd -t image -l //192.168.1.169/share/Fedora-Workstation-Live-42_Beta-1.4.x86_64.iso -u x -p x

# Normal share on strongbox
# This worked ok.
/opt/dell/srvadmin/bin/idracadm7 -u root -p calvin -r 192.168.1.115 vflashpartition create -i 1 -o f42b -e cddvd -t image -l //192.168.1.7/software/os_images/Linux/alma/AlmaLinux-9.5-x86_64/AlmaLinux-9.5-x86_64-boot.iso -u normaluser -p normalpassword

# The idracs still do SMB2 keepalives to the file server after image loading.
```

