# `git`

## Cherrypicking changes

`git add` allows to interactively select hunks to stage form different files:

```
git add -p
```

This allows to create atomic commits by interactively selecting changes to be staged.

## Large Repositories

When cloning large repositories, setting [`feature.manyFiles`] enables configuration options to improve performance:

```bash
git clone -c feature.manyFiles=true <REPO>
```

## Diff for staged files

`git diff` does not show staged files in the diff.

The `--staged` option can be used to show the diff for staged files:

```bash
git diff --staged
```

## Global ignore file

A global `git` ignore file can be created to ignore files across all repositories.
This is useful for files that are specific to a certain workflow (`.envrc`, ...) or editor (`.vscode`, `.idea`), 
but not relevant to the repository.

The global ignore file can be created by setting the `core.excludesfile` configuration:

```bash
git config --global core.excludesfile ~/.config/git/ignore
```

Typically, the file is stored under `~/.config/git/ignore`. To check if the global ignore file already exists, use:

```bash
git config --get core.excludesfile
```

[`feature.manyFiles`]: https://www.git-scm.com/docs/git-config/2.25.2#Documentation/git-config.txt-featuremanyFiles
