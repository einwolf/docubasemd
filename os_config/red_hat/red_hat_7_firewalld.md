# RHEL7 Firewalld

## Libvirt problem

RHEL 7 doesn't have the libvirt firewalld zone. This causes firewalld to block traffic on libvirt networks like vibr0. No zone is assigned to vibr0 by default.

```bash
# firewalld loses the association when restarted
firewall-cmd --zone internal --add-interface vibr0

# libvirt doesn't support <bridge zone='trusted'> in net xml

# Using network ips works
firewall-cmd --zone=trusted --add-source="192.168.122.0/24"
```
