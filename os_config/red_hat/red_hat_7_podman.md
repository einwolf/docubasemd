# RHEL 7 podman 1.6.4

## Move Container Storage

User config file is `~/.config/containers/storage.conf`. Default location is `~/.config/containers/storage`.

Root config file `/etc/containers/storage.conf`. Default location is `/var/lib/containers/storage/`.

Storage for container images is set by the storage.conf `graphroot` variable. Storage for volumes (including autocreated volumes) is set in libpod.conf `volume_path`.
