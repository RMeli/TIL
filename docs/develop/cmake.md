# CMake

## CMake and language servers

CMake works well with the [clangd] language server. It can automatially generate the `compile_commands.json` file,
which is used by clangd to provide code completion, diagnostics, and other features.

The `compile_commands.json` contains the exact comiler call for all translation units of the project.
 
To generate the `compile_commands.json` file, use the following [CMake] option:

```bash
-DCMAKE_EXPORT_COMPILE_COMMANDS=ON
```

[CMake]: https://cmake.org/
[clangd]: https://clangd.llvm.org/
