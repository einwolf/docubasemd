# SoftHSM

## SoftHSM 2

```bash
dnf install -y softhsm opensc
# softhsm2-util
# tokens dir /var/lib/softhsm/tokens
# tokens is owned by ods user
# usermod -G ods -a localsysadmin

# Change config file location
# ~/.config/softhsm2/softhsm2.conf
export SOFTHSM2_CONF=/etc/softhsm2.conf

# Show tokens
softhsm2-util --show-slots

# New token
# so = security officer, used to reinitialize token
# Using --free creates a slot number based on a random serial number
# Actually this doesn't use slot 0 either. Uses some random slot.
softhsm2-util --init-token --slot 0 --label test-token1 --pin 1234 --so-pin 1234

# Reinit and change label and user pin
softhsm2-util --init-token --token test-token1 --label test-token1 --pin 3333 --so-pin 1234

# Delete
# token or serial only
softhsm2-util --delete-token --token test-token1
```

## pkcs11

```bash
dnf install -y pcsc-lite

# Get info
pkcs11-tool --module /usr/lib64/pkcs11/libsofthsm2.so --show-info
pkcs11-tool --module /usr/lib64/pkcs11/libsofthsm2.so --list-slots

```
