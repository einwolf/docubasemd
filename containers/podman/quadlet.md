# Podman quadlet service files

```bash
man podman-systemd.unit 

# Manually run systemd unit generator
/usr/lib/systemd/system-generators/podman-system-generator --dryrun {--user}
/usr/libexec/podman/quadlet --dryrun {--user}

# Target specific directory
QUADLET_UNIT_DIRS=./quad /usr/lib/systemd/system-generators/podman-system-generator --dryrun {--user}

systemd-analyze {--user} --generators=true verify example.service
```

## Pod

Not sure if you can use more than one Pod. It reports "container state improper" and has
multiple containers listening on the same port problems.
So it seems like one pod with multiple container images.

## Kube

Test the quadlet with podman kube play. Quadlet services don't report errors worth a darn.

```bash
kube supports Network=host or Network=something.network
Using something.network does need a quadlet something.network file.
PublishPort doesn't work with Network=host.

PersistentVolumeClaim doesn't need a something.volume.
It will create a volume with the same name as the pvc.
```

## Network

```bash
network units correspond to podman network create.
service name is xxx-network.service
```
