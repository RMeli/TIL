# Configuration

## Message of the day

The contents of `/etc/motd` are displayed after a successful login,
just before the login shell is executed.

### Ubuntu

Ubuntu introduced the `update-motd` framework, by which the `motd` is
dynamically assembled from a collection of scripts at login.

Executable scripts in `/etc/update-motd.d/*` are executed by `pam_motd` as the root user at each login,
and this information is concatenated in `/var/run/motd`.
