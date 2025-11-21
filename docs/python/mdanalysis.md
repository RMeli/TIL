# MDAnalysis

## OpenMP on macOS

Apple's Clang compiler does not support [OpenMP], and `g++` on macOS is an alias for `clang++`. 
Therefore, compiling a simple OpenMP program with `g++ -fopenmp` fails on macOS.

```text
clang: error: unsupported option '-fopenmp'
clang: error: unsupported option '-fopenmp'
```

??? example "Simple OpenMP program"

    ```cpp
    #include <omp.h>

    int main(void) {
        omp_get_num_threads();
    }
    ```

`g++` on macOS is an alias for `clang++`.

### OpenMP Support with GCC

In order to use OpenMP on macOS, one needs to install `gcc`, for example via [Homebrew].

```bash
brew install gcc
```

MDAnalysis can then be installed with OpenMP support as follows:

```bash
CC=gcc-11 CXX=g++-11 pip install -e package
```

### Disabling OpenMP Support

OpenMP support can be explicitly disabled with

```bash
MDA_USE_OPENMP=FALSE CC=gcc-11 CXX=g++-11 pip install -e package
```

[MDanalysis]: https://www.mdanalysis.org/
[OpenMP]: https://www.openmp.org
[Homebrew]: https://brew.sh/
