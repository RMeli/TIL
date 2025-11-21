# Containers

## Install missing packages

If some packages are missing from the container, it is possible to install them
with the following workaround:

```bash
apt update && apt download <PACKAGE> # (1)!
dpkg -x <PACKAGE>.deb / # (2)!
```

1. Download the `.deb` package file for `<PACKAGE>`.
2. Extract the contents of the package into the container.

This is useful in situations where `apt install` can't be used.

## podman

* [podman]
* [NVIDIA Container Toolkit]

# GPU support

The [NVIDIA Container Toolkit] `nvidia-ctk` needs to be installed.
In order to run a `podman` container with GPU support, one needs to
[generate a CDI specification](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/cdi-support.html).

```bash
sudo nvidia-ctk cdi generate --output=/etc/cdi/nvidia.yaml
nvidia-ctk cid list
```

??? info "Container Device Interface"

    CDI is an open specification for container runtimes that abstracts what access to a device means,
    and standardizes access across container runtimes.

To run a `podman` container with GPU support, use the following:

```bash
podman run --device=nvidia.com/gpu=all <IMAGE> <COMMAND>
```

??? failure "Error: setting up CDI devices"

    The folloiwng error

    ```
    Error: setting up CDI devices: failed to inject devices: failed to stat CDI host device "/dev/nvidia-uvm": no such file or directory
    ```
    
    can be fixed by generating the CDI specification with `nvidia-ctk`, as shown above.


[NVIDIA Container Toolkit]: https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/index.html
[podman]: https://podman.io/
