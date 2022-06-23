# SSH Forced Password

If passwordless SSH connections fail with the following error

```text
Too many authentication failures
```

it is possible to force SSH to ask for a password with the following options:

```bash
ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no HOST
```
