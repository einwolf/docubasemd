# borg backup

```bash
# https://borgbackup.readthedocs.io/en/stable/quickstart.html

# Create repo
borg init --encryption=repokey /path/to/repo

# Create named archive
borg create /path/to/repo::Monday ~/src ~/Documents
borg create --stats --progress /path/to/repo::Tuesday ~/src ~/Documents

# List repos and archives
borg list /path/to/repo
borg list /path/to/repo::Monday

# Restore files
borg extract /path/to/repo::Monday # restores to original path
borg extract /path/to/repo::Tuesday /restore/path
borg mount /mnt/backup/borg_repo::myserver-system-2019-08-11 /mnt/borg

# Delete archives
borg delete /path/to/repo::Monday
borg prune --list --keep-daily 7 --keep-weekly 4 --keep-monthly 6 /path/to/repo
borg compact /path/to/repo

```

```bash
# Remote with ssh
borg create ssh://borg@host:/path/to/repo::Monday ~/src ~/Documents
borg mount ssh://borg@backup.example.org:2222/path/to/repo /mnt/borg
borg extract ssh://borg@backup.example.org:2222/path/to/repo

```
