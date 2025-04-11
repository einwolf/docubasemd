# idrac virtual media

Configuration / Virtual Media

This is also where the idrac drivers iso is mounted from.

Paste the url into the file path field
http://192.168.1.55/boot/osiso/Fedora-Workstation-Live-42-1.1.x86_64.iso
nfs - 192.168.1.55:/srv/www/osiso/Fedora-Server-dvd-x86_64-42-1.1.iso
smb - //192.168.1.55/

User and pass has limits like no @ or , can be used.

Set the boot to Virtual Network File 1 (or 2).

dd reads /dev/sr0 at about 8 MB/sec.

