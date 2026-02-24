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

!!! note

    `kill` sends the `SIGTERM` signal by default, which allows the process to terminate gracefully.

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

# Tracing system calls

`strace` allows to trace system calls and signals made by a process.

By default `strace` does not follow child processes, therefore the `-f` is often useful.

```
strace -f -o <STRACE_OUTPUT_FILE> <COMMAND>
```

`-e` can be used to filter the traced syscalls: `-e trace=process` traces only process-related syscalls.

```
strace -f -e trace=process -s 1024 -o <STRACE_OUTPUT_FILE> <COMMAND>
```

The `-s` option sets the maximum string size, which by default is relatively small.

!!! tip

    [`strace-process-tree`](https://github.com/mgedmin/strace-process-tree)
    allows to visualize the `strace -f` output as a process tree.

    ??? Example "`strace-process-tree` output"

        ```console
        $ strace-process-tree /tmp/trace.out
        25510 make binary-package
          ├─25511 /bin/sh -c 'dpkg-parsechangelog | awk '\''$1 == "Source:" { print $2 }'\'''
          │   ├─25512 dpkg-parsechangelog
          │   │   └─25514 tail -n 40 debian/changelog
          │   └─25513 awk '$1 == "Source:" { print $2 }'
          ├─25515 /bin/sh -c 'dpkg-parsechangelog | awk '\''$1 == "Version:" { print $2 }'\'''
          │   ├─25516 dpkg-parsechangelog
          │   │   └─25518 tail -n 40 debian/changelog
          │   └─25517 awk '$1 == "Version:" { print $2 }'
          ├─25519 /bin/sh -c 'dpkg-parsechangelog | grep ^Date: | cut -d: -f 2- | date --date="$(cat)" +%Y-%m-%d'
          │   ├─25520 dpkg-parsechangelog
          │   │   └─25525 tail -n 40 debian/changelog
          │   ├─25521 grep ^Date:
          │   ├─25522 cut -d: -f 2-
          │   └─25523 date --date=" Thu, 18 Jan 2018 23:39:51 +0200" +%Y-%m-%d
          │       └─25524 cat
          └─25526 /bin/sh -c 'dpkg-parsechangelog | awk '\''$1 == "Distribution:" { print $2 }'\'''
              ├─25527 dpkg-parsechangelog
              │   └─25529 tail -n 40 debian/changelog
              └─25528 awk '$1 == "Distribution:" { print $2 }'
        ```

## git-like patches

The `diff` command can be used to create git-like patches between two files or directories:

```bash
diff -uNr <OLD> <NEW> > changes.patch
```

This can be useful to generate patches for [Spack](../develop/spack.md).

## bwrap

`bwrap` ([bubblewrap]) allows to substitute a directory with another, which can be useful to test changes without modifying the original files.

```bash
bwrap --dev-bind / / --bind <NEW_DIR> <OLD_DIR> <COMMAND>
```

This is expecially useful to patch system files without modifying the original ones,
or temporarily modify Spack-installed files without modifying the Spack installation.

!!! warning
 
    `--dev-bind / /` allows the process to access the entire filesystem.
    This is necessary for commands to work properly, especially the shell.

    Withou `--dev-bind / /`, one would get errors like the following:

    ```
    bwrap: execvp bash: No such file or directory
    ```

??? example "Patch Spack-installed CUDA"

    On Ubuntu 25.10, `glibc`’s ``<math.h>`` declares `rsqrt` with `noexcept(true)`,
    but CUDA’s headers declare it without `noexcept`.

    To test a patch that adds `noexcept` to CUDA’s headers,
    one can use `bwrap` to substitute the original CUDA headers with modified the original Spack-installed ones.

    ```bash
    cp $(spack location cuda)/include/crt/* ~/tmp/cuda-include/crt
    # Modify the files in ~/tmp/cuda-include/crt/, such as math_functions.h
    
    bwrap --dev-bind / / --bind ~/tmp/cuda-include/crt $(spack location cuda)/include/crt -- zsh
    # The shell will have the modified files in place of the original ones
    ```

??? example "Modifying a squashfs image"

    To modify a squashfs image that has to be mounted,
    one can use `bwrap` to substitute the original image with a modified one.

    ```bash
    # Extract the original image
    unsquashfs <IMAGE> -d <DIRECTORY>

    # Modify the files in <DIRECTORY> as needed

    # Mount the modified image using bwrap
    bwrap --dev-bind / / --bind <DIRECTORY> <DIRECTORY> -- bash
    ```

    This avoids the need to repack the modified directory into a new squashfs image, which can be time-consuming.


[bubblewrap]: https://github.com/containers/bubblewrap
