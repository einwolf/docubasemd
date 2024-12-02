# LHC@Home projects

## SixTrack

Native linux from before VirtualBox business

## sixtracktest

Never had it run. Probably ok.

## CMS Simulation

Virtualbox only. No native.

## Theory Simulation

* Runs native or VirtualBox
* Native theory uses runc with cgroups=v1 limitation. Doesn't work due to cgroups=v2.
* VirtualBox is erroring saying hardware virtualization is not available. Nested virtualization is  enabled in ESXi.
* Needs cvms

## ATLAS Simulation

Can get working

* Workunits have large 1 gb download
* Runs native or VirtualBox
* Needs cvms
* Native is ok. Uses apptainer run from cvms
* VirtualBox has above problem

## ATLAS (long simulation)

Probably same as atlas, but no workunits available.
