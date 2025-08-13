# Containers at CSCS

## JFrog

### Pull a container from JFrog locally

1. Enable the VPN (if not on the CSCS network)
1. Log in to JFrog
   ```bash
   podman login --username <USERNAME> --password <PASSWORD> jfrog.svc.cscs.ch
   ```
   The password is the JFrog API key, which can be generated in the JFrog web interface:
   `User Menu > Edit Profile > API Key`
1. Pull the container
   ```bash
   podman image pull jfrog.svc.cscs.ch/<REPOSITORY>/<IMAGE>:<TAG>
   ```

## MPI Containers

### MPICH

The following containers were created by Andreas Fink.

#### Eiger (zen2)

```Dockerfile
FROM docker.io/ubuntu:24.04

ARG libfabric_version=1.22.0
ARG mpi_version=4.3.1
ARG osu_version=7.5.1

RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends build-essential ca-certificates automake autoconf libtool make gdb strace wget python3 git gfortran \
    && rm -rf /var/lib/apt/lists/*

RUN git clone https://github.com/hpc/xpmem \
    && cd xpmem/lib \
    && gcc -I../include -shared -o libxpmem.so.1 libxpmem.c \
    && ln -s libxpmem.so.1 libxpmem.so \
    && mv libxpmem.so* /usr/lib64 \
    && cp ../include/xpmem.h /usr/include/ \
    && ldconfig \
    && cd ../../ \
    && rm -Rf xpmem

RUN wget -q https://github.com/ofiwg/libfabric/archive/v${libfabric_version}.tar.gz \
    && tar xf v${libfabric_version}.tar.gz \
    && cd libfabric-${libfabric_version} \
    && ./autogen.sh \
    && ./configure --prefix=/usr \
    && make -j$(nproc) \
    && make install \
    && ldconfig \
    && cd .. \
    && rm -rf v${libfabric_version}.tar.gz libfabric-${libfabric_version}

RUN wget -q https://www.mpich.org/static/downloads/${mpi_version}/mpich-${mpi_version}.tar.gz \
    && tar xf mpich-${mpi_version}.tar.gz \
    && cd mpich-${mpi_version} \
    && ./autogen.sh \
    && ./configure --prefix=/usr --enable-fast=O3,ndebug --enable-fortran --enable-cxx --with-device=ch4:ofi --with-libfabric=/usr --with-xpmem=/usr \
    && make -j$(nproc) \
    && make install \
    && ldconfig \
    && cd .. \
    && rm -rf mpich-${mpi_version}.tar.gz mpich-${mpi_version}

RUN wget -q http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-v${osu_version}.tar.gz \
    && tar xf osu-micro-benchmarks-v${osu_version}.tar.gz \
    && cd osu-micro-benchmarks-v${osu_version} \
    && ./configure --prefix=/usr/local CC=$(which mpicc) CFLAGS=-O3 \
    && make -j$(nproc) \
    && make install \
    && cd .. \
    && rm -rf osu-micro-benchmarks-v${osu_version} osu-micro-benchmarks-v${osu_version}.tar.gz
```

The [container engine] will inject `xpmem` and `libfabric` into the container at runtime.

??? "Building and running the container"
   
    Build the container:

    ```bash
    podman build -f Dockerfile -t osu:latest .
    enroot import -o osu.sqfs podman://osu:latest # (1)!
    ```

    1. Import the container with [enroot] for running with the [container engine].

    Prepare the TOML file for the [container engine]:

    ```toml
    image = '<IMAGE>'
    mounts = ["/capstor/scratch/cscs/"]
    workdir = '<WORKDIR>'
    writable = true
    entrypoint = true
    annotations.com.hooks.cxi.enabled = 'true' # (1)!
    ```

    1. [CXI hook](https://docs.cscs.ch/software/container-engine/resource-hook/#hpe-slingshot-interconnect).

    Run the [OSU micro-benchmarks]:

    ```bash
    srun --mpi=pmi2 -p debug -N2 -n2 --environment=$PWD/osu.toml \
      /usr/local/libexec/osu-micro-benchmarks/mpi/pt2pt/osu_bw
    ```

#### Daint (GH200)

```Dockerfile
FROM docker.io/nvidia/cuda:12.8.1-devel-ubuntu24.04

ARG libfabric_version=1.22.0
ARG mpi_version=4.3.1
ARG osu_version=7.5.1

RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends build-essential ca-certificates automake autoconf libtool make gdb strace wget python3 git gfortran \
    && rm -rf /var/lib/apt/lists/*

RUN echo '/usr/local/cuda/lib64/stubs' > /etc/ld.so.conf.d/cuda_stubs.conf && ldconfig

RUN git clone https://github.com/hpc/xpmem \
    && cd xpmem/lib \
    && gcc -I../include -shared -o libxpmem.so.1 libxpmem.c \
    && ln -s libxpmem.so.1 libxpmem.so \
    && mv libxpmem.so* /usr/lib \
    && cp ../include/xpmem.h /usr/include/ \
    && ldconfig \
    && cd ../../ \
    && rm -Rf xpmem

RUN wget -q https://github.com/ofiwg/libfabric/archive/v${libfabric_version}.tar.gz \
    && tar xf v${libfabric_version}.tar.gz \
    && cd libfabric-${libfabric_version} \
    && ./autogen.sh \
    && ./configure --prefix=/usr --with-cuda=/usr/local/cuda \
    && make -j$(nproc) \
    && make install \
    && ldconfig \
    && cd .. \
    && rm -rf v${libfabric_version}.tar.gz libfabric-${libfabric_version}

RUN wget -q https://www.mpich.org/static/downloads/${mpi_version}/mpich-${mpi_version}.tar.gz \
    && tar xf mpich-${mpi_version}.tar.gz \
    && cd mpich-${mpi_version} \
    && ./autogen.sh \
    && ./configure --prefix=/usr --enable-fast=O3,ndebug --enable-fortran --enable-cxx --with-device=ch4:ofi --with-libfabric=/usr --with-xpmem=/usr --with-cuda=/usr/local/cuda \
    && make -j$(nproc) \
    && make install \
    && ldconfig \
    && cd .. \
    && rm -rf mpich-${mpi_version}.tar.gz mpich-${mpi_version}

RUN wget -q http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-v${osu_version}.tar.gz \
    && tar xf osu-micro-benchmarks-v${osu_version}.tar.gz \
    && cd osu-micro-benchmarks-v${osu_version} \
    && ./configure --prefix=/usr/local --with-cuda=/usr/local/cuda CC=$(which mpicc) CFLAGS=-O3 \
    && make -j$(nproc) \
    && make install \
    && cd .. \
    && rm -rf osu-micro-benchmarks-v${osu_version} osu-micro-benchmarks-v${osu_version}.tar.gz

RUN rm /etc/ld.so.conf.d/cuda_stubs.conf && ldconfig
```

??? "Building and running the container"
   
    Build the container:

    ```bash
    podman build -f Dockerfile -t osu:latest .
    enroot import -o osu.sqfs podman://osu:latest # (1)!
    ```

    1. Import the container with [enroot] for running with the [container engine].

    Prepare the TOML file for the [container engine]:

    ```toml
    image = '<IMAGE>'
    mounts = ["/capstor/scratch/cscs/"]
    workdir = '<WORKDIR>'
    writable = true
    entrypoint = true
    annotations.com.hooks.cxi.enabled = 'true' # (1)!
    ```

    1. [CXI hook](https://docs.cscs.ch/software/container-engine/resource-hook/#hpe-slingshot-interconnect).

    Run the [OSU micro-benchmarks]:

    ```bash
    srun --mpi=pmi2 -p debug -N2 -n2 --environment=$PWD/osu.toml \
      env MPIR_CVAR_CH4_OFI_ENABLE_HMEM=1 \ # (1)!
      /usr/local/libexec/osu-micro-benchmarks/mpi/pt2pt/osu_bw D D
    ```

    1. Enables GPU direct RDMA support in the provider.
       Equivalent to Cray-MPICH `MPICH_GPU_SUPPORT_ENABLED=1`.

[enroot]: https://github.com/NVIDIA/enroot
[container engine]: https://docs.cscs.ch/software/container-engine/

