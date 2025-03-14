# `conda`

## Move `.conda` Directory

### Problem

The `.conda` directory might get quite beefy over time. This might cause problems with disk space (for example on the `$HOME` directory on an HPC cluster) and will need to be moved.

### Solution

Move the `.conda` directory as usual:

```bash
mv $SOURCE/.conda $DESTINATION/.conda
```

Amend `conda` configuration:

```bash
conda config --remove pkgs_dirs $SOURCE/.conda/pkgs
conda config --add pkgs_dirs $DESTINATION/.conda/pkgs
conda config --remove envs_dirs $SOURCE/.conda/envs
conda config --add envs_dirs $DESTINATION/.conda/envs
```

Restart the shell. Everything should be good to go.

## Using `conda` with SLURM

### Problem

Many HPC clusters provide [modules] for [Anaconda]. However, the command `conda activate ENV` within a [SLURM] script fails with the following error:

```text
CommandNotFoundError: Your shell has not been properly configured to use 'conda activate'.
```

The problem is that the (user-specific) `.condarc` configuration file is not being loaded when running a [SLURM] script with `sbatch`.

### Solution

Use

```bash
#!/bin/bash -l
```

instead of `#!/bin/bash` at the top of the [SLURM] script. 

The `-l` option forces `bash` to act as a login shell (thus loading the user-specific `.condarc` file) instead of acting as a blank shell.

[modules]: http://modules.sourceforge.net/
[Anaconda]: https://www.anaconda.com/
[SLURM]: https://slurm.schedmd.com/
