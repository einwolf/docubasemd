# Supermicro IPMI virtual media for HTML client

The Supermicro html virtual media mounts iso files from Windows file shares.

## X11 AST2400

The X11 models using the AST2400 are very limited in the smb version it can use.
The AST2400 appears to be running Linux 2.6.x and uses a very old version of smbclient and cifs.
It supports NT LM 0.12 authentication only.

The samba 4.20.2 in RHEL 9 still supports this, but it will whine about it.

## Example smb.conf

Example smb.conf using localsysadmin as a user for the share.
Don't forget to add the user to samba's password db with `smbpasswd -a localsysadmin`.

```text
# smb.conf enabling NT LM1 auth for Supermicro's IPMI.
[global]
    workgroup = WORKGROUP
    security = user
    passdb backend = tdbsam
    
    server min protocol = NT1
    ntlm auth = ntlmv1-permitted
    lanman auth = yes
    
    guest account = localsysadmin
    map to guest = bad user

    printing = cups
    printcap name = cups
    load printers = no
    cups options = raw
    
    log level = 3 passdb:5 auth:5

[homes]
    comment = Home Directories
    valid users = %S, %D%w%S
    browseable = No
    read only = No
    inherit acls = Yes

[printers]
    comment = All Printers
    path = /var/tmp
    printable = Yes
    create mask = 0600
    browseable = No

[print$]
    comment = Printer Drivers
    path = /var/lib/samba/drivers
    write list = @printadmin root
    force group = @printadmin
    create mask = 0664
    directory mask = 0775
    
[share]
    path = /data/share/localsysadmin
    writeable = yes
    browseable = yes
    guest ok = yes
    force create mode = 0666
    force directory mode = 0777

```

Enter the configuration in the Virtual Media / CD-ROM image section as below.
The path to image is picky about the format.

```text
Share Host          192.168.1.167
Path to Image       \share\Fedora-Workstation-Live-x86_64-37-1.7.iso
User (optional)     user
Password (optional) password
```

Press the save and then mount button. If it worked status will look like this.

```text
Device 1    There is an iso file mounted.
Device 2    No disk emulation set.
Device 3    No disk emulation set.
```

Unchanged parameters will unmount. Not sure what the minimum number of parameters is.

The three devices are shared between the floppy and iso mounting.

Boot with the `ATEN Virtual CD-ROM Device` from the F11 boot selection. It will also show up in F12 BIOS boot options on the last page.
