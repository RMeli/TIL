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

## Conditional configuration

`git` supports [conditional configurations] to include configurations based on conditions.
A common use-case is to have different configurations for work and personal repositories.


!!! example "Separate work and personal configurations"

    If work repositories are under `~/git/work` and personal repositories are under `~/git/oss`,
    the following configuration can be used:

    ```ini
    [includeIf "gitdir:~/git/work/"]
        path = ~/git/work/.config
    [includeIf "gitdir:~/git/oss/"]
        path = ~/git/oss/.config
    ```
    === "`~/git/work/.config`"

        ```ini
        [user]
            name = John Doe
            email = john.doe@work.com
        ```

    === "`~/git/oss/.config`"

        ```ini
        [user]
            name = John Doe
            email = john.doe@oss.org
        ```

[conditional configurations]: https://git-scm.com/docs/git-config#_conditional_includes
[`feature.manyFiles`]: https://www.git-scm.com/docs/git-config/2.25.2#Documentation/git-config.txt-featuremanyFiles

