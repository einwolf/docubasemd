# Setup RHEL7 libvirt for kubernetes

This is mostly about networking setup.

* br-netfilter for firewalld/iptables to work on bridge interfaces
* Enable sysctls for routing and forwarding traffic

Ref https://wiki.libvirt.org/page/Net.bridge.bridge-nf-call_and_sysctl.conf

/etc/modules-load.d/br-netfilter.conf

```bash
br-netfilter
```

/etc/sysctl.d/ip-network.conf

```bash
net.ipv4.ip_forward=1
```

/etc/sysctl.d/br-netfilter.conf

```bash
net.bridge.bridge-nf-call-arptables=0
net.bridge.bridge-nf-call-iptables=0
net.bridge.bridge-nf-call-ip6tables=0
```

```yaml
# ansible-playbook -v -i 192.168.122.61, setup-libvirt.yaml -b -u cluster
# ansible-playbook -v -i localhost, -e ansible_connection=local setup-libvirt.yaml -b -K
---
- hosts: all
  gather_facts: False

  tasks:
  - name: Load br_netfilter module
    modprobe:
      name: br_netfilter
      state: present
    register: br_netfilter

  - name: Add br_netfilter into /etc/modules-load.d/br-netfilter.conf
    copy:
      dest: /etc/modules-load.d/br-netfilter.conf
      content: |
        br-netfilter
      owner: root
      group: root
      mode: 0644

  - name: sysctl net.ipv4.ip_forward=1
    sysctl:
      name: net.ipv4.ip_forward
      value: "1"
      sysctl_file: /etc/sysctl.d/ip-network.conf
      state: present
      reload: yes

  - name: sysctl bridge-nf-call-arptables/iptables/ip6tables=1
    sysctl:
      name: "{{ item }}"
      state: present
      value: "0"
      sysctl_file: /etc/sysctl.d/bridge-nf-call.conf
      reload: yes
    with_items:
      - net.bridge.bridge-nf-call-arptables
      - net.bridge.bridge-nf-call-iptables
      - net.bridge.bridge-nf-call-ip6tables
```
