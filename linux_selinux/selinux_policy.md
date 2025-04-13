# Selinux

## seinfo

```bash
# Display all types in policy
seinfo -t
```

## apol

Graphical policy browser

```bash
dnf install -y setools-gui graphviz python3-pygraphviz
# Run apol and open /etc/selinux/targeted/policy/policy.33
```

## Search selinux policy

```bash
# List all allow rules (long)
sesearch --allow

# List all allow rules with source of ping_t
sesearch --allow -s ping_t
```
