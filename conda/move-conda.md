# Move `.conda` Directory

## Problem

The `.conda` directory might get quite beefy over time. This might cause problems with disk space (for example on the `$HOME` directory on an HPC cluster) and will need to be moved.

## Solution

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
