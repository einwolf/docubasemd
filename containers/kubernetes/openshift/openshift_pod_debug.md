# OpenShift Pod Debugging

Debug pod requires an image pull. For CRC, make sure proxy environment is set and `crc config set http-proxy`, https-proxy, and no-proxy are set.

```bash
# Debug pod
oc get nodes
oc debug nodes/node-name
```

## ssh to CodeReady containers node

```bash
crc ip
ssh -i ~/.crc/cache/crc_libvirt_4.6.15/id_ecdsa_crc core@192.168.130.11
ssh -i ~/.crc/machines/crc/id_edcsa core@192.168.130.11
```
