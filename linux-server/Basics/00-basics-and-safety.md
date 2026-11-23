# 00 — Basics and production safety

[← Home](../README.md) · [Next: User management →](01-user-management.md)

## What is what?

| Word | Simple explanation | Example |
|---|---|---|
| User | An identity that can own files and run processes | `devops01` |
| Group | A way to grant the same permissions to multiple users | `app-team` |
| Owner | User who owns a file/folder | `devops01` |
| Permission | Rules for reading, changing, running or entering files/folders | `rwxr-x---` |
| `sudo` | Run an allowed command with elevated privileges | `sudo systemctl status ssh` |
| SSH | Encrypted remote login to a server | `ssh devops01@203.0.113.10` |
| Public key | Shareable SSH key copied to the server | `id_ed25519_company.pub` |
| Private key | Secret SSH key stays on the employee's device | `id_ed25519_company` |

## Before running commands

1. Check the server hostname and the current identity.

```bash
hostnamectl
whoami
id
pwd
```

2. Check OS and SSH service.

```bash
cat /etc/os-release
sudo systemctl status ssh --no-pager
```

3. If remotely connected, **leave the existing SSH session open** while configuring a new account; have a console or EC2 recovery path.
4. Record the approved change, account owner, group memberships, and rollback plan.
5. Confirm you are pasting commands into the **server terminal** versus the **employee's laptop terminal**.

## Understanding the prompt

Commands beginning with `sudo` need an already authorized administrative account. The `$` and `#` sometimes seen in tutorials are terminal prompts; do **not** type them. Commands in this project are intended to be copied without prompt symbols.

## Before changing SSH server settings

```bash
# Back up the current main SSH configuration
sudo cp -a /etc/ssh/sshd_config "/etc/ssh/sshd_config.bak.$(date +%Y%m%d-%H%M%S)"

# Inspect included files as well
sudo ls -la /etc/ssh/sshd_config.d/

# Test syntax (does NOT test successful authentication)
sudo sshd -t
```

When you change SSH settings, inspect the **effective** result with `sudo sshd -T`, test syntax with `sudo sshd -t`, reload cautiously, and attempt a **new** login from a different terminal. Ubuntu's `/etc/ssh/sshd_config` can include `/etc/ssh/sshd_config.d/*.conf`; an earlier directive may win over a later one. Some Ubuntu releases also use SSH socket activation; don't blindly change the listening port.

## Command risk labels

- **READ:** Checks that do not intentionally change system settings: `id`, `getent`, `ls -l`, `sshd -T`.
- **CHANGE:** Creates/modifies accounts, group membership and access: `adduser`, `usermod`, `chmod`, `visudo`.
- **DISRUPTIVE:** Can terminate access, remove data or break services: `userdel -r`, `loginctl terminate-user`, SSH authentication changes, recursive ownership changes.
