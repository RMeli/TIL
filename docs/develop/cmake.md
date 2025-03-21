# CMake

## CMake and language servers

CMake works well with the [clangd] language server. It can automatially generate the `compile_commands.json` file,
which is used by clangd to provide code completion, diagnostics, and other features.

The `compile_commands.json` contains the exact comiler call for all translation units of the project.
 
To generate the `compile_commands.json` file, use the following [CMake] option:

```bash
-DCMAKE_EXPORT_COMPILE_COMMANDS=ON
```

## Ccache

To use [Ccache] (compiler cache) with [CMake], one can set the following environment or [CMake] variable:

```bash
CMAKE_<LANG>_COMPILER_LAUNCHER=ccache
```

Supported languages are the following: `C`, `CXX`, `Fortran`, `CUDA`, `HIP`.

### Project-wide setting

For a project-wide setting, one can add the following to the `CMakeLists.txt` file:

```cmake
find_program(CCACHE_PROGRAM ccache)
if(CCACHE_PROGRAM)
  set(CMAKE_<LANG>_COMPILER_LAUNCHER "${CCACHE_PROGRAM}")
endif()
```

### Target-specific setting

For a specific target, the `<LANG>_COMPILER_LAUNCHER` property can be set:

```cmake
find_program(CCACHE_PROGRAM ccache)
if(CCACHE_PROGRAM)
  set_target_properties(<TARGET> PROPERTIES <LANG>_COMPILER_LAUNCHER ${CCACHE_PROGRAM})
endif()
```

[Ccache]: https://ccache.dev/
[CMake]: https://cmake.org/
[clangd]: https://clangd.llvm.org/
