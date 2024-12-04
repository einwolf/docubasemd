# xcp-ng and secure boot

## Secure boot keys

Download the default secure boot keys from Microsoft and install them into the server pool.

```bash
[15:18 xcp3 ~]# secureboot-certs install
Downloading https://www.microsoft.com/pkiops/certs/MicCorKEKCA2011_2011-06-24.crt...
Downloading https://www.microsoft.com/pkiops/certs/MicCorUEFCA2011_2011-06-27.crt...
Downloading https://www.microsoft.com/pkiops/certs/MicWinProPCA2011_2011-10-19.crt...
Downloading https://uefi.org/sites/default/files/resources/dbxupdate_x64.bin...
Successfully installed custom certificates to the XAPI DB for pool.
```
