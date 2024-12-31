# Xen Orchestra Community Edition

## VM Import

The node runtime in the container needs a lot of memory. It has 41 GB virtual allocation.
Too little memory causes VMware imports to run slow and fail.
For a specific import, slow is 10+ hours import. Normal is 4 hours.

The XOA appliance has 2 GB ram allocated and has similar problems.
Using XOA-CE with 8 GB was not working. Had to make it 64 GB to import from VMware without problems.

Not sure on the web page OVA or VMDK import. Uploading 100 GB to the web page doesn't work well.

`xe vm.import` only takes XVA files. Not sure if other command line import is available.
`xo-upload-ova` exists but not sure where to run it.

Raw vmdk import suggested to convert vmdk to vhd, copy it to storage, and setup a sr for a new VM.
Name it any uuid.
`https://docs.xcp-ng.org/installation/migrate-to-xcp-ng/`
`qemu-img convert -f vmdk -O vpc myVMwaredisk.vmdk /run/sr-mount/<SR UUID>/$(uuidgen).vhd`
