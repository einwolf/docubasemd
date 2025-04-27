# netlabel

```bash
dnf install -y netlabel_tools setools-console policycoreutils-python-utils
```

Note netlabel_peer_type selinux attribute.

## Selinux type range

Sensitivity is 16 values s0 (low) to s15 (high).

Category is 1024 values c0 to c1023. This is a multi set field so c5-c19 and c15,c62 are valid.

## CIPSO tag types

Commercial IP Security option (CIPSO)

Netlabel is tied to CIPSO tag types.
[CIPSO draft RFC](https://www.ietf.org/archive/id/draft-ietf-cipso-ipsecurity-01.txt)
See Domain of Interpretation (DOI) section to set tag type.

DOI is unsigned 32 bit integer 0-4,294,967,295. 0 is reserved.

*Compared to SElinux*

DOI trans is probably needed to use sensitivity 16-255 and category > 1023 to
bring them into SElinux range.

No description on what happens if categories do not fit in the type label.

*CIPSO Type 1*

Sensivity is an unsigned 8 bit integer 0-255. 0 is low and 255 is high.

Category is a boolean bitmap field with max length 8 octets. This allows 0-239.
SElinux category c240 and up are sliently dropped.

*CIPSO Type 2*

Sensivity is an unsigned 8 bit integer 0-255. 0 is low and 255 is high.

Category is a unsigned 16 bit integer 0-65534. 65535 is reserved.
1 to 15 categories may be used. I expect the 16th category and up to be silently dropped.
Categories must be listed in ascending order.

*CIPSO Type 5*

Sensivity is an unsigned 8 bit integer 0-255. 0 is low and 255 is high.

Category is in pairs of unsigned 16 bit integers specified high to low.
There is a max of 7 category pairs.
Categories must be listed in decending order.
The last category's end is assumed to be 0 and is implicit.

Not sure what happens if more ranges than possible are needed.

## CALIPSO tag types

Common Architecture Label IPv6 Security Option
[CALIPSO RFC](https://www.rfc-editor.org/rfc/rfc5570.html)

There is one format.

Maximum compartment length is measured in unsigned 8 bit octet representing 0-255 32 bit words.
There's a statement on ipv6 64 bit padding to reconsile the 32 bit length vs 64 bit compartment bitmap.

Sensivity is an unsigned 8 bit integer 0-255. 0 is low and 255 is high.

Category is called compartment and is a bitmap of 64 bit words. Number of words is in compartment length.

Releasability is included in the compartment field as inverted compartments.
What does that mean? How are category or releasability type determined in the same bitmap?
Wait, the RFC says it's not described on purpose. So it's basically a vendor specific field.

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

# doi needs to be set or Linux will reject CIPSO tags with ICMP parameter problem.
# IP error pointer = 22. 22 Bytes in from ipv4 header is CIPSO DOI field.
# Just the doi needs to be added with matching tags

# Sends tag 1, accepts 1 and 2
netlabelctl cipso add pass doi:1 tags:1,2

# Sends type 2, accepts only type 2.
# Mismatches with above as it rejects type 1.
netlabelctl cipso add pass doi:1 tags:2


```bash
# Map outgoing domains to doi
netlabelctl map -p list

# Outgoing domains will use cipso doi 1
# domain = selinux type
# Remember the sensitvity and category used is from the socket;s selinux label
netlabelctl map add default address:192.168.201.2 protocol:cipso,1

# Outdoing http_t will use cipso doi 2
netlabelctl map add domain:http_t protocol:cipso,2

# Label ping_t going to 192.168.201.0/24 with cipso doi 3.
netlabelctl map add domain:ping_t address:192.168.201.0/24 protocol:cipso,3

# Deletes all rules for domain type
netlabelctl map del domain:http_t

```

```bash
# Set default (fallback) labels for incoming unlabeled traffic
# Label all (unlabeled) traffic
# Need to use full label and it must be valid for policy (or it errors).
netlabelctl unlbl add default label:system_u:object_r:http_t:s0:c0

# Label subnet 192.168.201.0/24 traffic
netlabelctl unlbl add default address:192.168.201.0/24 label:system_u:object_r:http_t:s0:c0

# Can scope to interface. Address still required.
netlabelctl unlbl add interface:eth0 address:192.168.201.0/24 label:system_u:object_r:http_t:s0:c0

# Delete rule by rule
netlabelctl unlbl del default address:192.168.201.0/24
netlabelctl unlbl del interface:eth0 address:192.168.201.0/24

```

## Loopback with full labels

```bash
# Local ipv4 loopback with full mix
# Shows up in wireshark as tag type 6 with 6 byte tag data
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

# More generally, you can't combine match all and address selectors.
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
# Unlabeled tagging
netlabelctl unlbl add default address:192.168.201.0/24

# You can only delete all the map rules at once
netlabelctl map del default
netlabelctl map del domain:apache_t

# Delete DOI one at a time
# You can't delete doi if there are map rules using it
netlabelctl cipso del doi:1
netlabelctl cipso del doi:2

```

Not sure if map is strictly output and unlbl is strictly input.
If map uses protocol:unlbl does it go to unlbl rules?
I don't think it does.

```bash
# loopback traffic goes on doi:10000
# default (unmatched label) traffic on 0.0.0.0/0 (anywhere else) is unlabeled
# default (unmatched label) traffic on 192.168.201.0/24 doi:1 cipso tag type 1
# Traffic from source domain ping_t on 192.168.201.0/24 doi:2 cipso tag type 2
# netlabelctl cipso add pass doi:2 tags:1,2 -> accepts/sends tag type 1 and 2

# type 1 bitmap
# type 2 enumerated

netlabelctl cipso add pass doi:1 tags:1
netlabelctl cipso add pass doi:2 tags:2
netlabelctl cipso add local doi:20000

netlabelctl map del default
netlabelctl map add default address:0.0.0.0/0 protocol:unlbl                  
                                                        
netlabelctl map add default address:::/0 protocol:unlbl
netlabelctl map add default address:127.0.0.0/8 protocol:cipso,20000
netlabelctl map add default address:192.168.201.0/24 protocol:cipso,1

netlabelctl map add domain:ping_t address:192.168.201.0/24 protocol:cipso,2

netlabelctl unlbl add default address:192.168.201.0/24 label:unconfined_u:unconfined_r:unconfined_t:s0:c1

netlabelctl -p cipso list
netlabelctl -p map list
netlabelctl -p unlbl list

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

```bash
# reset
netlabelctl unlbl del default address:192.168.201.0/24

netlabelctl map del default
netlabelctl map del domain:ping_t

netlabelctl cipso del doi:1
netlabelctl cipso del doi:2
netlabelctl cipso del doi:20000

netlabelctl map add default protocol:unlbl

netlabelctl -p cipso list
netlabelctl -p map list
netlabelctl -p unlbl list

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
