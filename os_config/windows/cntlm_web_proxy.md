# NTLM Authentication Proxy

http://cntlm.sourceforge.net/

For everything that can't "just do Windows".

```text
get hash: ./cntlm.exe -u user@domain -H
test: ./cntlm.exe -c cntlm.conf -M https://www.google.com/
foreground run: ./cntlm.exe -v -c cntlm.conf -f
```
