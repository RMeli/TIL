# CUDA

## CUDA compatibility and PTX

The [CUDA compatibility] documentation denotes the following issue with PTX
and minor version compatibility:

> Applications that compile device code to PTX will not work on older drivers.
> If the application requires PTX then administrators have to upgrade the installed driver. [...]

Several applications perform JIT compilation of device code to PTX,
and this limitation is problematic when old drivers are installed on a system.

Forward compatibility is mainly intended to support applications built on newer CUDA Toolkits
to run on systems installed with an older NVIDIA Linux GPU driver from different major release families.
However, it also allows JIT compilation to PTX on older drivers.


!!! example "Installing forward compatibility package"
    Download and extract a CUDA driver supporting the target CUDA version:

    ```bash
    wget https://us.download.nvidia.com/tesla/575.57.08/NVIDIA-Linux-aarch64-575.57.08.run
    bash NVIDIA-Linux-aarch64-575.57.08.run -x
    ```

    Files will be extracted in `NVIDIA-Linux-aarch64-575.57.08`.

    Copy the following files from the extracted folder into a separate folder
    (ssay `compat-cu129`) and create the appropriate symlinks

    ```bash
    mkdir compat-cuda128

    cp NVIDIA-Linux-aarch64-575.57.08/libcudadebugger.so.575.57.08 compat-cuda129/
    cp NVIDIA-Linux-aarch64-575.57.08/libcuda.so.575.57.08 compat-cuda129/
    cp NVIDIA-Linux-aarch64-575.57.08/libnvidia-nvvm.so.575.57.08 compat-cuda129/
    cp NVIDIA-Linux-aarch64-575.57.08/libnvidia-ptxjitcompiler.so.575.57.08 compat-cuda129/

    cd compat-cuda129

    ln -s libcudadebugger.so.575.57.08 libcudadebugger.so.1
    ln -s libcuda.so.575.57.08 libcuda.so
    ln -s libcuda.so.575.57.08 libcuda.so.1
    ln -s libnvidia-nvvm.so.575.57.08 libnvidia-nvvm.so
    ln -s libnvidia-nvvm.so.575.57.08 libnvidia-nvvm.so.4
    ln -s libnvidia-ptxjitcompiler.so.575.57.08 libnvidia-ptxjitcompiler.so.1
    ```

    Finally, set `LD_LIBRARY_PATH`:

    ```
    export LD_LIBRARY_PATH=$PWD/compat-cuda129/:$LD_LIBRARY_PATH
    ```


[CUDA compatibility]: https://docs.nvidia.com/deploy/cuda-compatibility/index.html
