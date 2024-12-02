# Ansible collections and galaxy

## References

Install galaxy collection including requirements.yaml

https://docs.ansible.com/ansible/latest/user_guide/collections_using.html

Create galaxy collection

https://docs.ansible.com/ansible/latest/dev_guide/developing_collections.html
https://docs.ansible.com/ansible/latest/dev_guide/developing_collections_structure.html
https://docs.ansible.com/ansible/latest/dev_guide/collections_galaxy_meta.html

Article about using downloaded collection tars

https://www.redhat.com/sysadmin/install-ansible-disconnected-node

## Configuration

Default for collections_path is `collections_path=~/.ansible/collections:/usr/share/ansible/collections`

Ansible will also scan python sys.path (`collections_scan_sys_path=True`)

```bash
ansible.cfg
[defaults]
inventory = ./inventory 
collections_path = ./collections
nocows=True
retry_files_enabled = False
command_warnings=False
```

## Basic Commands

```bash
# Install from galaxy.ansible.com into specific directory
ansible-galaxy collection install community.kubernetes -p ./collections

# Install from tar
ansible-galaxy collection install community-kubernetes-1.2.0.tar.gz -p ./collections/

# Manual extract needs a ansible_collections/author/name path
mkdir -p collections/ansible_collections/community/kubernetes
tar -xf ~/Downloads/community-kubernetes-1.2.0.tar.gz -C collections/ansible_collections/community/kubernetes

# Galaxy commands
ansible-galaxy collection list
```

## Guided Tutorial

```bash
ansible-galaxy collection init my_namespace.my_collection
```
