# GitHub Secret Files

GitHub Actions might require a secret file. Such file could be stored encrypted in the GitHub repository and the decryption passphrase can be stored as a [GitHub Secret](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets).

## Encrypting Secret File with GPG

Encrypt file with [GPG](https://gnupg.org/):

```bash
gpg --symmetric --cipher-algo AES256 FILE
```

The passphrase used at this step is required to decrypt the file and can be stored as a GitHub Secret.

## Decryption Script

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

## GitHub Action

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
