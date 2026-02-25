# Secure SHell protocol (SSH protocol)

## Allow incoming SSH conncections from local network on Ubuntu

!!! warning

    If connecting from macOS,
    make sure the terminal application is allowed to discover devices on the local network.

Install `openssh-server`:

```bash
sudo apt update
sudo apt install openssh-server
sudo systemctl status ssh
```

Enable firewall and open port for SSH:

```bash
sudo ufw enable && sudo ufw status
sudo ufw allow <PORT>
sudo ufw status verbose
```

??? note "SSH port"

    By default, SSH uses port `22`.

    ```bash
    sudo ufw allow ssh
    ```
    enables port `22`.

Modify `/etc/ssh/sshd_config` to listen on `<PORT>`:

```diff
-#Port 22
+Port <PORT>
```

Then, restart the service:

```bash
sudo sudo systemctl restart sshd
sudo systemctl status sshd  
```

The daemon should be listening to `<PORT>`.

### Copy SSH key

With the above configuration is possible to use password autentication.
Copy a SSH key, to allow passwrodless authentication.

```bash
ssh-copy-id -o PasswordAuthentication=yes -i ~/.ssh/<key>.pub -p <PORT> <HOST>
```

### SSH Hardening

Ensure the following on `/etc/ssh/sshd_config`:

```bash
Protocol 2 # (1)!
PermitRootLogin no # (2)!
PasswordAuthentication no
AuthenticationMethods publickey
AllowUsers <USER> # (3)!
MaxSessions 2 # (4)!
```

1. Enforce SSH protocol 2 (improved security over protocol 1)
2. Disable `root` login, only allow non-privileged users to authenticate
3. Restrict access to specific users
4. Limit number of concuirrent SSH sessions

