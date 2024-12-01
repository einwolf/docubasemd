# Xcp-ng rename host interfaces

12/1/2024
Xcp-ng 8.3
https://xcp-ng.org/docs/networking.html#manage-physical-nics

```bash
[09:29 xcp3 ~]# interface-rename --help
Usage: interface-rename --rename|--list|--update <args>|--reset-to-install [-v] [-d]

Utility for managing the naming of physical network interfaces.  It is used to
undo the damage of race condition for device drivers grabbing eth names on
boot, taking into account naming policies provided at install time.  In
addition, it implements sensible policies when network hardware changes, and
the ability for manual alteration of the policies after install.

Options:
  --version             show program's version number and exit
  -h, --help            show this help message and exit
  -v, --verbose         increase logging
  -d, --dry-run         dry run - don't write any state back to disk

  Actions:
    Exactly one action is expected

    -r, --rename        rename physical interfaces.  It is not safe to rename
                        interfaces which have traffic passing, or higher level
                        networking constructs on them (bonds/bridges/etc).
                        Use at your own risk after boot
    -l, --list          list current physical device information in a concise
                        manner as a reference for --update
    -u, --update        manually update the order of devices.  <args> should
                        be one or more <target eth name>=MAC|PCI|Phys|"SMBios"
    --reset-to-install  reset configuration to install state
```

## Process

eth2 -> eth4
eth3 -> eth5

```bash
# Down interface to rename
ifconfig eth2 down
ifconfig eth3 down

# Set interface name for mac address
# Suspect it may do udev rules from /etc/udev/rules.d but it looks like
# a script /etc/udev/scripts/net-rename-sideways.sh attached to eth* does it.
interface-rename --list
interface-rename --update eth4=<eth2 mac> eth5=<eth3 mac>
INFO     [2024-12-01 10:00:02] Performing manual update of rules.  Not actually renaming interfaces
INFO     [2024-12-01 10:00:02] All done

# Forget the xcp-ng physical interface config for eth2 and eth3
# xe will tab complete uuids
xe pif-list
xe pif-forget uuid=<eth2 uuid>
xe pif-forget uuid=<eth3 uuid>
# No output

# Reboot the host to get it to rescan NICs
reboot

# Up the new interface names
ifconfig eth4 up
ifconfig eth5 up

# Get the uuid for the host
xe host-list
# uuid=<host uuid>

# Add new interface names to xcp-ng config
xe pif-introduce device=eth4 host-uuid=<host uuid> mac=<eth4 mac>
xe pif-introduce device=eth5 host-uuid=<host uuid> mac=<eth5 mac>
# Prints pif uuid

# The eth2 and eth3 pool wide networks get converted to private networks
network-list
xe network-destroy uuid=<network uuid>
```
