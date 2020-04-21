# Sync Remote Folder

> `rsync` is an open source utility that provides fast incremental file transfer.

Sync a remote folder locally:

```bash
rsync -avzhP user@host:HOST_PATH LOCAL_PATH
```

## Options

* `-a`: archive mode; same as `-rlptgoD` (no `-H`)
* `-v`: increase verbosity
* `-h`: output numbers in a human-readable format
* `-z`: compress file data during the transfer
* `-P`: keep partially transferred files and show progress during transfer
