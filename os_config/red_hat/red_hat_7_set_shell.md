# Set default shell in RHEL7

## Current Version

RHEL 7.9 with xrdp 0.9.16 from epel7 works with ~.Xsession. Xrdp works better than it used to.

Example .Xsession

```bash
#!/bin/bash
exec gnome-session
```

.Xclients is used also. Not sure if it's a version change thing. Tested in 0.9.17.

## Set user's default graphical shell through settings default

This is useful for xrdp where it doesn't run gdm to use its shell selection popup menu.

Make a file at `/var/lib/AccountService/users/` with the user name. Copy the contents of one of the desktop files below.

Example: `/var/lib/AccountService/users/<username>`

## RHEL docs reference

What is GNOME Classic?
https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/desktop_migration_and_administration_guide/what-is-gnome-classic

User Sessions
https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/desktop_migration_and_administration_guide/user-sessions#configuring-user-default-session

## Example Session file

Session for gnome shell.

```ini
[User]
Language=
XSession=gnome
```

Session for gnome-classic as created by default.

```ini
[User]
Lanauges=
XSession=gnome-classic
SystemAccount=false
```
