# `conda` with SLURM

## Problem

Many HPC clusters provide [modules](http://modules.sourceforge.net/) for [Anaconda](https://www.anaconda.com/). However, the command `conda activate ENV` within a [SLURM](https://slurm.schedmd.com/) script fails with the following error:

```text
CommandNotFoundError: Your shell has not been properly configured to use 'conda activate'.
```

The problem is that the (user-specific) `.condarc` configuration file is not being loaded when running a [SLURM](https://slurm.schedmd.com/) script with `sbatch`.

## Solution

Use

```bash
#!/bin/bash -l
```

instead of `#!/bin/bash` at the top of the [SLURM](https://slurm.schedmd.com/) script. 

### Explanation

The `-l` option forces `bash` to act as a login shell (thus loading the user-specific `.condarc` file) instead of acting as a blank shell.


`man bash` documentation for the `-l` option:

```text
-l        Make bash act as if it had been invoked as a login shell (see INVOCATION below).
```
