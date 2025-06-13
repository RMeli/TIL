# Useful commands

## Kill unresponsive process

An unresponsive processe/service ignores the `kill` command. 
In order to shut down the process immediately use

```bash
kill -9 PID
```

where `PID` is the process ID.

This is equivalent to

```bash
kill -SIGKILL PID
```

The `kill -SIGKILL` command bypasses the standard shutdown routine and unsaved data will be lost.

## Remove broken symbolic links

```bash
find <DIR> -xtype l -delete
```

## Sycalls ID to name mapping

The following command shows the mapping of syscall IDs to their names:

```bash
ausyscall --dump
```

??? note "Installation"

    `ausyscall` is part of the `audit` package.

    ```bash
    sudo apt install auditd
    ```
