# Secure SHell protocol (SSH protocol)

## Force password

If passwordless SSH connections fail with the following error

```text
Too many authentication failures
```

it is possible to force SSH to ask for a password with the following options:

```bash
ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no HOST
```

## Passwordless access

### Generate private/public keys pair

```bash
ssh-keygen -t rsa
```

## Add public key to remote server

```bash
ssh-copy-id -i ~/.ssh/KEY_FILE_NAME USER@HOST
```
