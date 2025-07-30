# GitHub

## List files in active PRs

To apply repository-wide automated changes (such as formatting) in active repositories,
it is useful to list files in active PRs to minimize the impact of such changes.

The [GitHub CLI] can be used to obtain such information.
The following extracts information, in JSON format, about 100 open PRs:

```bash
gh pr list -L 100 --json number,updatedAt,createdAt,title
```

[jq] can then be used to interact with the JSON output.

To extract the list of PR numbers with updates after a certain date one can use the folloiwng query:

```bash
gh pr list -L 500 --json number,updatedAt,createdAt,title \
  | jq '[.[] | select(.updatedAt > ("2025-07-01"))]' \
  | jq '.[] | .number'
```

With the PR numbers at hand, one can list the modified files in each PR using [gh] again:

```bash
gh pr diff --name-only <PR_NUMBER>
```

??? note "Credits"

    This workflow was originally developed by [Egor Marin],
    who helped with the automatic formatting of the whole [MDAnalysis] code base.

## Secret files

GitHub Actions might require a secret file. 
Such file could be stored encrypted in the GitHub repository and the decryption passphrase can be stored as a [GitHub Secret].

### Encrypting Secret File with GPG

Encrypt file with [GPG]:

```bash
gpg --symmetric --cipher-algo AES256 FILE
```

The passphrase used at this step is required to decrypt the file and can be stored as a GitHub Secret.

### Decryption Secret File

```bash
#!/bin/sh

mkdir $HOME/secrets

gpg --quiet --batch --yes --decrypt --passphrase="$SECRET_PASSPHRASE" \
    --output $HOME/secrets/FILE FILE.gpg
```

The shell script has to  be executable when pushed to GitHub:

```bash
chmod +x decrypt_secret.sh
```

### GitHub Action

```yaml
name: Decrypt Secret File

on: push

jobs:
  my-job:
    name: Decrypt Secret File
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Decrypt Secret File
        run: ./.github/scripts/decrypt_secret.sh
        env:
          SECRET_PASSPHRASE: ${{ secrets.SECRET_PASSPHRASE }}
```

[GitHub Secret]: https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets
[GPG]: https://gnupg.org/
[GitHub CLI]: https://cli.github.com/
[jq]: https://jqlang.org
[Egor Marin]: https://github.com/marinegor
[MDAnalysis]: https://www.mdanalysis.org/
