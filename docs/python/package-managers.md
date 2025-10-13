# Python package managers

## Spack

Spack installs every Python package in its own predix, 
in contrast to `pip` which installs packages into the same prefix.

Instead of repeated `spack load` commands to load all the necessary Python packages,
one can use environments instead and load a view of the environment instead:

```bash
spack activate -p -v <VIEW> <PATH_TO_ENV>
```

It is also possible to use a virtual environment created in the view:

```bash
source <PATH_TO_VIEW>/bin/activate
```

## Move `.conda` directory

The `.conda` directory might get quite beefy over time.
This might cause problems with disk space (for example on the `$HOME` directory on an HPC cluster)
and will need to be moved.

It is possible to move the `.conda` directory as usual:

```bash
mv $SOURCE/.conda $DESTINATION/.conda
```

and then amend `conda` configuration to point to the new location:

```bash
conda config --remove pkgs_dirs $SOURCE/.conda/pkgs
conda config --add pkgs_dirs $DESTINATION/.conda/pkgs
conda config --remove envs_dirs $SOURCE/.conda/envs
conda config --add envs_dirs $DESTINATION/.conda/envs
```

The shell need to be restarted for the changes to take effect.

## Using `conda` with SLURM

Many HPC clusters provide [modules] for [Anaconda].
However, the command `conda activate ENV` within a [SLURM] script fails with the following error:

```text
CommandNotFoundError: Your shell has not been properly configured to use 'conda activate'.
```

The problem is that the (user-specific) `.condarc` configuration file is not being loaded when running a [SLURM] script with `sbatch`.

To circumvent this issue, use

```bash
#!/bin/bash -l
```

instead of `#!/bin/bash` at the top of the [SLURM] script. 

The `-l` option forces `bash` to act as a login shell (thus loading the user-specific `.condarc` file)
instead of acting as a blank shell.

[modules]: http://modules.sourceforge.net/
[Anaconda]: https://www.anaconda.com/
[SLURM]: https://slurm.schedmd.com/
[Spack]: https://spack.io/
