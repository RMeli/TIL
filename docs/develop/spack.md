# Spack

## Testing locally with Spack

The [Spack] package manager is written in Python, therefore it can be used interactively from a Python
shell:

```console
> spack python
```

### Concretize and inspect a spec

A [Spack spec] can be concretized and inspected from the Python shell:

```python
from spack.spec import Spec
from spack.concretize import concretize_one

s = Spec("spfft ^[virtuals=fftw-api] nvpl-fft") # (1)!
sc = concretize_one(s) # (2)!
```

1. Define the Spack spec to concretize.
2. Concretize the spec.

One can then inspect the concretized spec, for example:

```python
sc["fftw-api"].libs.ld_flags
sc["fftw-api"].headers
```


## Conflicts

The following conflicts are equivalent:

```python
conflicts("+rocm +cuda")
conflicts("+rocm", when="+cuda")
conflicts("+cuda", when="+rocm")
```

The first option is cleaner, shorter, and less asymmetric.

## Ccache

To use [Ccache] (compiler cache) with [Spack], one can set the following in `~/.spack/config.yaml`
(or [Spack]'s default `etc/spack/config.yaml`):

```yaml
config:
    ccache: true
```

! warning

    Some packages with compiler wrappers (e.g., Kokkos) may not work correctly with Cache enabled.

## Patches

To generate a patch for a package, one can use the [`diff` command](../linux/commands.md#git-like-patches).

By default, Spack applies patches with `patch -p1`, therefore patches should be
modified so that file paths start with `a/` and `b/`, followed by the source code root directory.

## Spack views for development tools

[Spack environment views] are a way to create a single directory that contains all the dependencies of a package.
This is useful for easily installing and accessing development tools (such as editors, languages, etc.).

```yaml
spack:
  specs:
    - <SPEC1>
    - <SPEC2>
  concretizer:
    unify: true
  view:
    default:
      root: <VIEW_PATH>
```

??? example "Development tools"

    ```yaml
    spack:
      specs:
        - libtree
        - tmux
        - direnv
        - htop +hwloc
        - neovim
        - llvm
        - ripgrep
        - fzf
        - bat
        - ruby
        - go
        - rust +dev ~docs
        - node-js
        - npm
        - ccache
        - python
        - py-uv
      concretizer:
        unify: true
      packages:
        python:
          require:
            - "@3.11"
        llvm:
          require: 
            - "targets=x86" # Select one target to speedup compilation, doen't matter which one
            - "@18"
            - "~gold"
            - "~libomptarget"
      view:
        default:
	  root: ~/aarch64-dev.view
          link: roots
    ```

!!! warning "Views and the number of symlinks"

    Views can create a large number of symlinks, which can cause issues with some file systems.

    ??? example "Count the number of symlinks"

        ```bash
        find <PATH> -type l | wc -l
        ```

   To avoid creating too many symlinks, one can use `link: roots`:

   ```yaml
   view:
     default:
       root: ~/aarch64-dev.view
       link: roots
   ```

## Use Spack to install dependencies and run CMake

The following script can be used to install dependencies and run CMake for a given develop spec:

```bash
#!/bin/bash

# The script needs to be sourced to work correctly
# Use return instead of exit to return to the shell

help() {
	echo "Usage: source spackdev.sh <spack-env> <spack-spec>"
	echo "  spack-env: Spack environment (path)"
	echo "  spack-spec: Spack spec"
}

error_dev(){
	echo "ERROR┌ ${1} is not a 'develop' spec."
	echo "ERROR└  Make sure you are using the correct Spack environment and spec."
	return 1
}

# Warn if build_stage is not set in the Spack environment
warn_bs() {
	echo "WARN┌ You are using a standard 'build_stage' directory."
	echo "WARN| Consider adding the following to your Spack environment:"
	echo "WARN|"
	echo "WARN|     config:"
	echo "WARN└	  build_stage: <path-to-git-repo>/spack-build-stage/"
}

if [[ $# -ne 2 ]]; then
	help
	return
fi

SPACK_ENV=$1
SPACK_SPEC=$2

if command -v ccache 2>&1 > /dev/null; then
   export CMAKE_CXX_COMPILER_LAUNCHER=ccache
   export CMAKE_C_COMPILER_LAUNCHER=ccache
   export CMAKE_Fortran_COMPILER_LAUNCHER=ccache
   export CMAKE_CUDA_COMPILER_LAUNCHER=ccache
   export CMAKE_HIP_COMPILER_LAUNCHER=ccache
fi

# WARN: This only work if $SPACK_SPEC is just after the 'develop' key
grep -A 2 "develop:" "${SPACK_ENV}/spack.yaml" | grep -q "${SPACK_SPEC}:" || error_dev "${SPACK_SPEC}" || return
grep -q "build_stage:" "${SPACK_ENV}/spack.yaml" || warn_bs

spack -e "${SPACK_ENV}" clean || return # (1)!
spack -e "${SPACK_ENV}" concretize -f || return # (2)!
spack -e "${SPACK_ENV}" install --until=cmake --test=root --keep-stage || return # (3)!

SPACK_SOURCE_DIR=$(spack -e "${SPACK_ENV}" location --source-dir "${SPACK_SPEC}")
SPACK_BUILD_DIR=$(spack -e "${SPACK_ENV}" location --build-dir "${SPACK_SPEC}")

SPACK_ENV_NAME=$(basename "${SPACK_ENV}")
ENVRC_TMP="/tmp/.envrc-${SPACK_ENV_NAME}-${SPACK_SPEC}"
spack -e "${SPACK_ENV}" build-env --dump "${ENVRC_TMP}" "${SPACK_SPEC}" || return # (4)!
echo "SPACK_BUILD_DIR=\"${SPACK_BUILD_DIR}\"; export SPACK_BUILD_DIR" >> "${ENVRC_TMP}"
echo "SPACK_SOURCE_DIR=\"${SPACK_SOURCE_DIR}\"; export SPACK_SOURCE_DIR" >> "${ENVRC_TMP}"

cp "${ENVRC_TMP}" "${SPACK_BUILD_DIR}/.envrc"
direnv allow "${SPACK_BUILD_DIR}"

# Add .envrc in SPACK_SOURCE_DIR so that nvim integrated terminal is correctly set up
 mv "${ENVRC_TMP}" "${SPACK_SOURCE_DIR}/.envrc"
 direnv allow "${SPACK_SOURCE_DIR}"

# Be friendly to LSPs
mkdir -p "${SPACK_SOURCE_DIR}/build"
ln -sf "${SPACK_BUILD_DIR}/compile_commands.json" "${SPACK_SOURCE_DIR}/build/compile_commands.json" # (5)!

# Remove broken symlinks (from previous builds)
find "${SPACK_SOURCE_DIR}" -xtype l -delete
	
pushd "${SPACK_SOURCE_DIR}" || return
```

1. Clean the Spack environment.
2. Concretize the environment.
3. Install the dependencies and run CMake.
4. Dump the build environment to an `.envrc` file. This is used by [direnv] to set up the environment.
5. Create a symlink to the `compile_commands.json` file in the source directory. This is useful for LSPs.

!!! note

    The script is meant to be sourced (`source ...`), not executed.

[Ccache]: https://ccache.dev/
[Spack]: https://spack.io
[Spack spec]: https://spack.readthedocs.io/en/latest/packaging_guide.html#spack-specs
[direnv]: https://direnv.net/
[Spack environment views]: https://spack.readthedocs.io/en/latest/environments.html#environment-views
