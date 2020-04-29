# SSH Passwordless Access

## Private/Public Keys Pair

Generate a private/public pair of SSH keys:

```bash
ssh-keygen -t rsa
```

## Add Public Key to Remote Server

Add public key to authorized keys on remote server:

```bash
ssh-copy-id -i ~/.ssh/KEY_FILE_NAME USER@HOST
```
