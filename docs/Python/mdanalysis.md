# MDAnalysis

## OpenMP on macOS

[MDanalysis](https://www.mdanalysis.org/) does not compile with OpenMP support on macOS.

### Why?

When building, [MDanalysis](https://www.mdanalysis.org/) checks if the following program can be compiled with the current compiler:

```cpp
#include <omp.h>

int main(void) {
    omp_get_num_threads();
}
```

On macOS, compiling such program with `g++ -fopenmp` fails with the following error:

```text
clang: error: unsupported option '-fopenmp'
clang: error: unsupported option '-fopenmp'
```

`g++` on macOS is an alias for `clang++`.

### OpenMP Support with GCC

Install GCC using `brew install gcc` (or any other package manager).

```bash
CC=gcc-11 CXX=g++-11 pip install -e package
```

### Disabling OpenMP Support

OpenMP support can be explicitly disabled with

```bash
MDA_USE_OPENMP=FALSE CC=gcc-11 CXX=g++-11 pip install -e package
```
