# `rsync`

> `rsync` is an open source utility that provides fast incremental file transfer.

## Sync remote molder

Sync a remote folder locally:

```bash
rsync -avzhP user@host:HOST_PATH LOCAL_PATH
```

`-a` enables archive mode (same as `-rlptgoD` without `-H`), `-v` increases verbosity, `-h` outputs numbers in a human-readable format, `-z` compresses file data during the transfer, and `-P` keeps partially transferred files and shows progress during the transfer.
