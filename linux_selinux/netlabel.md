# netlabel

```bash
dnf install -y netlabel_tools setools-console policycoreutils-python-utils
```

Note netlabel_peer_type selinux attribute.

## getpeercon_server

```bash
https://github.com/pcmoore/misc-getpeercon_server.git
dnf install -y libselinux-devel selinux-policy-devel
```

## netlabelctl

```bash
# Enable doi
# cipso and cipsov4 are interchangable
# tag type 1 and 2 are 2 formats covering the same thing.
# tag type 5 is associated with RIPSO
# -p on list commands is "human readable"
netlabelctl -p cipso list
netlabelctl cipso add pass doi:1 tags:1

# Outgoing domain http_t will be labeld with cipso doi 1.
# domain = selinux type
netlabelctl map -p list
netlabelctl map add domain:http_t protocol:cipso,1
netlabelctl map add domain:ping_t address:192.168.201.0/24 protocol:cipso,1

netlabelctl map del domain:http_t

```

```bash
# Set default (fallback) labels on all traffic or subnet traffic
# address:0.0.0.0/0
netlabelctl unlbl add default label:http_t
netlabelctl unlbl add default address:192.168.201.0/24 label:http_t

```

## Loopback has full labels

```bash
# Local ipv4 loopback with full labels
# Can't duplicate doi and protocol so add local at 10000 (unlikely doi number)
netlabelctl cipso add pass doi:1 tags:1
netlabelctl cipso add local doi:10000

# Need to remove default mapping or else it matches everything
# The other map add defaults error until it's removed
# del default deletes all the domain:default entries.
# Commands like del default address:192.168.201.2 gives a command error
netlabelctl map del default
netlabelctl map add default address:0.0.0.0/0 protocol:unlbl                  
                                                        
netlabelctl map add default address:::/0 protocol:unlbl
netlabelctl map add default address:127.0.0.0/8 protocol:cipso,10000
netlabelctl map add default address:192.168.201.2 protocol:cipso,1
netlabelctl -p map list

# More generally, you can't mix match all and address selectors.
# Use address:0.0.0.0/0 for default other host traffic
netlabelctl map add domain:apache_t protocol:cipsov4,16
netlabelctl map del domain:apache_t
netlabelctl map add domain:apache_t address:0.0.0.0/0 
```

It says `waiting .. connect(127.0.0.1,NO_CONTEXT)` when there is no context.
Instead it can be configured to send the socket's context on loopback connections.
This shows up in wireshark lo traffic as
IPv4 Commercial Security Option DOI 10000 tag type 6. (6 bytes)

```bash
[localsysadmin@netse2 misc-getpeercon_server]$ ./src/getpeercon_server 5555
-> running as unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
-> creating socket ... ok
-> listening on TCP port 5555 ... ok
-> waiting ... connect(127.0.0.1,unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023)
hi
```

### Loopback fallback label

```bash
# Instead of keeping the label set a (static) default label for unlabeled traffic
netlabelctl unlbl add interface:lo address:127.0.0.0/8 label:system_u:object_r:netlabel_peer_t:s0
```

## Sample netlabel setup

```bash
# Delete DOI one at a time
# You can't delete doi if there are map rules using it
netlabelctl cipso del doi:1

# You can only delete all the map rules at once
netlabelctl map del default
netlabelctl map del domain:apache_t

```

```bash
# loopback traffic goes on doi:10000
# default (unmatched label) traffic on 0.0.0.0/0 (anywhere else) is unlabeled
# default (unmatched label) traffic on 192.168.201.0/24 doi:1 cipso tag type 1
# Traffic from source domain ping_t on 192.168.201.0/24 doi:2 cipso tag type 1,2
# netlabelctl cipso add pass doi:2 tags:1,2 -> uses tag type 1

# type 1 bitmap
# type 2 enumerated

netlabelctl cipso add pass doi:1 tags:1
netlabelctl cipso add pass doi:2 tags:2
netlabelctl cipso add local doi:10000

netlabelctl map del default
netlabelctl map add default address:0.0.0.0/0 protocol:unlbl                  
                                                        
netlabelctl map add default address:::/0 protocol:unlbl
netlabelctl map add default address:127.0.0.0/8 protocol:cipso,10000
netlabelctl map add default address:192.168.201.0/24 protocol:cipso,1

netlabelctl map add domain:ping_t address:192.168.201.0/24 protocol:cipso,2

netlabelctl -p cipso list
netlabelctl -p map list

# default CIPSO to 192.168.201.1 transmits tag type 1 doi 1 domain net_peer_t sensitivity 0

# ping runs as unconfined_t so use runcon
# runcon -t ping_t ping 192.168.201.1 transmits doi:2 tag type 1
# ping prints
# From 192.168.201.1 icmp_seq=1 Parameter problem: pointer = 22
# and gets parameter error icmp responses probably because I didn't configure
# 192.168.201.1 netlabel correctly

# If you set netlabel policies the same on source and destination then
# ping sends correct responses and this
# 64 bytes from 192.168.201.1: icmp_seq=3 ttl=64 time=1.71 ms
# unknown option 86

# Cipso is ip option 134. Not sure how that translates to 86.
```

## netlabel.service

Put commands in `/etc/netlabel.rules` and `enable netlabel.service` to apply at boot.

## Docs

### CIPSO Commercial Internet Protocol Security Option

[CIPSO draft RFC](https://datatracker.ietf.org/doc/html/draft-ietf-cipso-ipsecurity-01)

### CALIPSO Common Architecture Label IPv6 Security Option

[CALIPSO RFC 5570](https://datatracker.ietf.org/doc/html/rfc5570)

### Oracle Trusted Solaris

[How to Configure an IPv6 CIPSO Network in Trusted Extensions](https://docs.oracle.com/cd/E37838_01/html/E61029/txconf-35.html)
