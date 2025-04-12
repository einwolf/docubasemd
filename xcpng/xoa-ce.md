# Xen Orchestra Community Edition

## VM Import

The node runtime in the container needs a lot of memory. It has 41 GB virtual allocation.
Too little memory causes VMware imports to run slow and fail.
For a specific import, slow is 10+ hours import. Normal is 4 hours.

The XOA appliance has 2 GB ram allocated and has similar problems.
Using XOA-CE with 8 GB was not working. Had to make it 64 GB to import from VMware without problems.

Imports go through the xoa management interface.

`xe vm.import` only takes XVA files. Not sure if other command line import is available.
`xo-upload-ova` exists but not sure where to run it from.

Raw vmdk import suggested to convert vmdk to vhd, copy it to storage, and setup a sr for a new VM.
Name it any uuid.
`https://docs.xcp-ng.org/installation/migrate-to-xcp-ng/`
`qemu-img convert -f vmdk -O vpc myVMwaredisk.vmdk /run/sr-mount/<SR UUID>/$(uuidgen).vhd`

## VM Feedback

The Import button on the VMware import turns into a spinner and prevents doing more imports.
Not sure if it's needed to wait for this. It does stop spinning eventually before
the import errors or finishes.

The import job XO task progress bar doesn't move. Look at the status light.
Pool tasks cover hd import progress.

## OVA import

```bash
# OVA import is tested against virtualbox only.
# It doesn't support compression.

# VMware ovftool using VM folder view references
# no --compression=6
ovftool --noSSLVerify --overwrite --diskmode=thin --parallelThreads=4
"vi://user:pass@vcenter/<DataCenter>/vm/<Folder>/<vm name>" <vm name>.ova
```

## Clearing dead imports

```bash
xo-cli rest del tasks
xe-toolstack-restart
# Delete the VMs starting with [Importing...] in the name.
```
