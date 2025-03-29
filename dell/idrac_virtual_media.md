# idrac virtual media

Configuration / Virtual Media

This is also where the idrac drivers iso is mounted from.

Paste the url into the file path field
http://192.168.1.55/boot/osiso/Fedora-Workstation-Live-42_Beta-1.4.x86_64.iso
nfs://
smb://

Set the boot to Virtual Network File 1 (or 2).

dd reads /dev/sr0 at about 8 MB/sec.

