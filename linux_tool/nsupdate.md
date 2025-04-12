# nsupdate

```bash
# debug logging
# Mostly shows function call sequence though
nsupdate -L 10
# debug command shows answer in bind/dig format
```

## Windows DNS

Windows needs kerberos authentication. It doesn't use TSIG.
Insecure updates needed.

```bash
nsupdate
> debug
> update delete test1.example.com A
> update add test1.ioncollector.com 86400 A 192.168.1.73
> send
> answer
# or exit when record doesn't exist
nsupdate
> prereq nxdomain test1.example.com
> update add test1.ioncollector.com 86400 A 192.168.1.73
> send
```
