# XCP-NG Virtual Machine settings

```bash
# Set memory limits
# Static is the min/max presented to the VM
# Dynamic is an adjustable allocation within the static limits
# 0 < memory-static-min <= memory-dynamic-min <= memory-dynamic-max <= memory-static-max
xe vm-memory-limits-set uuid=<uuid> static-min=<nn>GiB/MiB dynamic-min=<nn>GiB/MiB dynamic-max=<nn>GiB/MiB     static-max=<nn>GiB/MiB
xe vm-memory-limits-set uuid=120513b6-68ec-c0eb-ba87-b4936bbbc66f static-min=64GiB dynamic-min=64GiB   dynamic-max=64GiB static-max=64GiB

# Get memory limits
xe vm-param-get uuid=<uuid> param-name=memory-static-min
xe vm-param-get uuid=<uuid> param-name=memory-static-max
xe vm-param-get uuid=<uuid> param-name=memory-dynamic-min
xe vm-param-get uuid=<uuid> param-name=memory-dynamic-man
```
