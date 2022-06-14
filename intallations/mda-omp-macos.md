# MDAnalysis with OpenMP on macOS

[MDanalysis](https://www.mdanalysis.org/) does not compile with OpenMP support on macOS.

## Why?

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

## Using OpenMP Support

### GCC

Install GCC using `brew`:

```bash
brew install gcc
```

`brew`-installed GCC compilers are named as `gcc-X`/`g++-X` where `X` is the major version number.

### Build MDAnalysis with GCC

```bash
CC=gcc-11 CXX=g++-11 pip install -e package
```

OpenMP support can be explicitly disabled with

```bash
MDA_USE_OPENMP=FALSE CC=gcc-11 CXX=g++-11 pip install -e package
```
