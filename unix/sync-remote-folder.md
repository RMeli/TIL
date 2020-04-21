# Sync Remote Folder

> `rsync` is an open source utility that provides fast incremental file transfer.

Sync a remote folder locally:

```bash
rsync -avzh user@host:HOST_PATH LOCAL_PATH
```

## Options

* `-a`: archive mode (recursive, preserves links, timestamps, ...)
* `-v`: increase verbosity
* `-h`: output numbers in a human-readable format
* `-z`: compress file data during the transfer
