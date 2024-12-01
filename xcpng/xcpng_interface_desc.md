# Xcp-ng host interfaces

Xcp-ng pools can't tolerate interfaces with different uses.
That is, if eth0 is management it must be management on all hosts in pool.
VMs can have a max of 7 ethernet devices connected

## 4 stack

Interface|Speed|Use
---|---|---
eth0|1Gb|Management
eth1
eth2
eth3
---|---|---
eth4|10Gb|Storage
eth5
eth6
eth7
---|---|---
eth8|25Gb|Storage
eth9
eth10
eth11

## 8 stack

Interface|Speed|Use
---|---|---
eth0|1Gb|Management
eth1
eth2
eth3
eth4
eth5
eth6
eth7
---|---|---
eth8|10Gb|Storage
eth9
eth10
eth11
eth12
eth13
eth14
eth15
---|---|---
eth16|25Gb|Storage
eth17
eth18
eth19
eth20
eth21
eth22
eth23
