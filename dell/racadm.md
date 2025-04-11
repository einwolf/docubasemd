# Dell racadm

```bash
/opt/dell/srvadmin/sbin/racadm help

# local
/opt/dell/srvadmin/sbin/racadm getsensorinfo

# remote
/opt/dell/srvadmin/sbin/racadm -i -r 192.168.1.115 getsensorinfo
```

## vflash

Server needs to be booted at least once or the vflash status will be wrong.
Apparently no command line to attach/detach images.

I didn't get this to work consistently.
It did not load from samba1 at all. Stays at 30% and times out or goes to 100% and doesn't show partition in vflash.
It would load half the image from strongbox. It would go to 100%.

idrac 7.00.00.174 on the r640 is working.
Nah, it still cut off proxmox 8.4 at 1500 mb.

```bash
/opt/dell/srvadmin/sbin/racadm -u root -p calvin -r 192.168.1.115 vflashsd initialize
/opt/dell/srvadmin/sbin/racadm -u root -p calvin -r 192.168.1.115 vflashsd status

/opt/dell/srvadmin/sbin/racadm -u root -p calvin -r 192.168.1.115 vflashpartition list
/opt/dell/srvadmin/sbin/racadm -u root -p calvin -r 192.168.1.115 vflashpartition status -a
/opt/dell/srvadmin/sbin/racadm -u root -p calvin -r 192.168.1.115 vflashpartition delete -i 1

# Needs to use a guest SMB share. Not sure on supported SMB versions.
# Guest share user needed no password set (smbpasswd -n localsysadmin)
# Second -u and -p are user and password for SMB share.
# Don't appear to be able to stop upload.
# vflashpartition status -a shows progress
# Progress starts at 30% and jumps to 100% on success or failure.

# Loads at 4 MB/sec
# Both servers spew NT_ACCESS_DENIED errors from flush requests.
# Image still loads ok.

# The idracs still do SMB2 keepalives to the file server after image loading.

# This has problems where it times out and doesn't finish.

# Guest share on samba1
/opt/dell/srvadmin/sbin/racadm -u root -p calvin -r 192.168.1.115 vflashpartition create -i 1 -o f42 -e cddvd -t image -l //192.168.1.169/share/Fedora-Workstation-Live-42-1.1.x86_64.iso -u x -p x

/opt/dell/srvadmin/sbin/racadm -u root -p calvin -r 192.168.1.115 vflashpartition create -i 2 -o pve84 -e cddvd -t image -l //192.168.1.169/share/proxmox-ve_8.4-1.iso -u x -p x

```

