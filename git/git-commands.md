# `git` Commands

## `git add`

### Interactive Patches

Interactively select hunks to stage form different files:

```
git add -p
```

This allows to create atomic commits by interactively selecting changes to be staged.

## `git clone`

### Large Repositories

When cloning large repositories, setting [`feature.manyFiles`](https://www.git-scm.com/docs/git-config/2.25.2#Documentation/git-config.txt-featuremanyFiles) enables configuration options to improve performance:

```bash
git clone -c feature.manyFiles=true <REPO>
```

## `git diff`

### Diff for Staged Files

`git diff` does not show staged files in the diff. Show the diff of staged files:

```bash
git diff --staged
# git diff --cached

```
