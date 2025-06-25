# Building ipxe

## Packages needed

```bash
dnf group install -y c-development
dnf install -y xz-devel
```

## Building

```bash
git clone https://www.github.com/ipxe/ipxe
cd ipxe/src
# git checkout v1.0.0 # not compile
# git checkout v1.20.1 # not compile
# git checkout v1.21.1 # not compile
git checkout master # does compile
# Edit config/general.h and config/console.h
make
# These are bios pxe images
make bin/ipxe.iso bin/ipxe.usb bin/undionly.kpxe
# These are uefi images
make bin-x86_64-efi/ipxe.efi bin-x86_64-efi/ipxe.iso bin-x86_64-efi/ipxe.usb bin-x86_64-efi/snponly.efi
# bin-x86_64-efi/ipxe.efi -> ipxe-x86_64.efi
```

```bash
cp /home/localsysadmin/github/ipxe/src/bin-x86_64-efi/ipxe.efi ipxe-x86_64.efi
cp /home/localsysadmin/github/ipxe/src/bin-x86_64-efi/snponly.efi snponly.efi
```
