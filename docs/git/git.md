# `git`

## Worktrees

[`git worktree`](https://git-scm.com/docs/git-worktree) allows to have multiple working directories for a single repository.
A worktree is the same as a normal repository, with the caviet that it is not possible to checkeout the same branch in multiple worktrees.

This is useful for working on multiple features in parallel, and avoids the need to stash changes or create multiple clones of the repository.

### Create a new worktree

```bash
git worktree add -b <BRANCH> <PATH>
```

!!! tip "Convention"

    Clone the repository as `<REPO>.<DEFAULT_BRANCH>` and create worktrees for features as `<REPO>.<FEATURE_BRANCH>`.
    This is my own convention and it is not a standard.

## Aliases

`git` allows to create aliases for common commands.

Aliases can be created in the global configuration as follows:

```bash
git config --global alias.<ALIAS> <COMMAND>
```

!!! tip "My configuration"
    
    ```ini
    [alias]
        aa = add --all
        co = checkout
        cob = checkout -b
        cs = commit -signed --signoff
        discard = reset HEAD --hard
        fp = push --force-with-lease
        lol = log --graph --decorate --pretty=oneline --abbrev-commit
        lola = log --graph --decorate --pretty=oneline --abbrev-commit --all
        pr = "!f() { git fetch --force origin pull/$1/head:pr-$1; }; f"
        ps = push --signed
        root = rev-parse --show-toplevel
        st = status
        stag = tag -l --sort=v:refname
        uncommit = reset --soft HEAD^
        unstage = reset HEAD --
        wt = worktree
    ```

    ??? info "`--force-with-lease`"
    
        `--force-with-lease` is a safer alternative to `--force` that does not overwrite changes on the remote branch.

## Identity

### Signing commits

`git` supports signing commits with [GPG] keys.

??? example "Create GPG keys"

    ```bash
    gpg --default-new-key-algo rsa4096 --gen-key
    gpg --armor --export <KEY_ID> # (1)!
    ```

    1. Export public key. This can be used to add the key to GitHub, etc.

??? example "List available GPG keys"

    ```bash
    gpg --list-secret-keys --keyid-format=long
    ```

```bash
git config --global --unset gpg.format # (1)!
git config --global user.signingkey 3AA5C34371567BD2
```

1. One could use different key formats, such as SSH keys. Here we want to use [GPG] keys.

Signing keys can also be added to specific configurations or repositories.
See [Conditional configuration](#conditional-configuration) for separate work and personal configurations.

### Signoff commits

`git commit` supports the `--signoff` option to add a signoff line to the commit message.

This is useful to indicate that the author has the right to submit the code.

```bash
git commit --signoff
```

## Bisection

`git bisect` allows to find the commit that introduced a bug by performing a binary search.

To starti bisecting, use:

```bash
git bisect start
```

Then mark the current commit as bad:

```bash
git bisect bad
```
and mark a known good commit:

```bash
git bisect good <COMMIT>
```

`git bisect` will then checkout a commit in the middle of the range and ask to mark it as good or bad.
This process is repeated until the commit that introduced the bug is found.

### Correct a bisection mistake

If a commit is marked as good or bad by mistake, it can be corrected with the following steps:

1. Save the current bisection log to a file
   ```bash
   git bisect log > bisect.log
   ```

2. Reset the bisection state
   ```bash
   git bisect reset
   ```

3. Amend the bisection log file to remove the incorrect entry

4. Restart the bisection with the corrected log
   ```bash
   git bisect replay bisect.log
   ```

??? example "`git bisect` log"

    ```text
    git bisect start
    # status: waiting for both good and bad commits
    # good: [0128dc24c6d018b61ceaac080640014e1d5ec344] Update README
    git bisect good 0128dc24c6d018b61ceaac080640014e1d5ec344
    # bad: [25935e1a7e022ede9fd71bd86dcbaa7a3f1846b7] Merge pull request #85 from gdonval/fix-malloc
    git bisect bad 25935e1a7e022ede9fd71bd86dcbaa7a3f1846b7
    # bad: [637a6e5f2953263d4f05574c8d6037356a81b9ea] Merge pull request #65 from Reference-ScaLAPACK/weslleyspereira-fix-cmake-requirements
    git bisect bad 637a6e5f2953263d4f05574c8d6037356a81b9ea
    # good: [681a6cf436360a3f5121f224a026150795283e56] Merge pull request #56 from weslleyspereira/try-CI
    git bisect good 681a6cf436360a3f5121f224a026150795283e56
    ```

!!! tip

    One could add the following [git alias](git.md/#aliases) to undo the last action automatically:

    ```
    alias bisect-undo='git bisect log | head -n -2 > /tmp/git-fixed-bisect.txt && git bisect reset && git bisect replay /tmp/git-fixed-bisect.txt && rm /tmp/git-fixed-bisect.txt'
    ```

## Cherrypicking changes

`git add` allows to interactively select hunks to stage form different files:

```
git add -p
```

This allows to create atomic commits by interactively selecting changes to be staged.

## Large Repositories

When cloning large repositories,
setting [`feature.manyFiles`](https://www.git-scm.com/docs/git-config/2.25.2#Documentation/git-config.txt-featuremanyFiles) enables configuration options to improve performance:

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

??? tip "Excluded files"

    ```
    .envrc
    .vscode/
    .idea/
    core*
    .null-ls_*
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

## Clean up

Some workflows produce a lot of temporary files that are not tracked by `git`.
It is sometimes useful to clean up these files to have the repository in a clean state.
To clean up untracked files, use:

```bash
git clean -fd
```

The command above will remove all untracked files and directories.

!!! danger

    Be careful with `git clean -fd` as it will remove all untracked files and directories.
    It is recommended to perform a dry run with

    ```
    git clean -nfd
    ```

    to see what files will be removed.

## Revert changes from a specific file

The `git revert` command can be used to revert a specific commit.
However, this reverts all the files changed in that commit.
To revert changes from a specific file, use the `git checkout` command:

```bash
git checkout <COMMIT>~ -- <FILE>
```

!!! note "`~`"

    `<rev>~[<n>]` refers to the `<n>`-th generation ancestor of `<rev>`.

To revert the changes introduced by a specific commit `<COMMIT>`,
we checkout the file from the commit before it (`<COMMIT>~`).

[conditional configurations]: https://git-scm.com/docs/git-config#_conditional_includes
[feature.manyFiles]: https://www.git-scm.com/docs/git-config/2.25.2#Documentation/git-config.txt-featuremanyFiles
[GPG]: https://gnupg.org
