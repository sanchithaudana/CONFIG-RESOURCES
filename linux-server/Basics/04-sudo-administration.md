# 04 — Sudo (administrator) access

[← Home](../README.md) · [← File permissions](03-file-permissions-and-acl.md) · [Next: SSH →](05-ssh-access.md)

`sudo` allows an authorized person to run permitted commands with another user's privileges (usually root). It is **separate from SSH login access**.

## Who has sudo? (**READ**)

```bash
getent group sudo                    # Accounts listed in the sudo group
id devops01                         # User's actual groups
sudo -l -U devops01                  # Rules effective for that user (as admin)
```

An account could have sudo through another rule even if it is not in the `sudo` group. Use `sudo -l -U` for a complete check.

## Full admin access: only when approved (**CHANGE**)

```bash
sudo usermod -aG sudo devops01
id devops01
```

Test in a **new login**:

```bash
sudo -l
sudo whoami                          # Expected: root, if the user has full sudo
```

Full sudo can usually be used to take over the server. Do not grant it as a shortcut to let someone edit one project directory.

## Remove broad sudo access (**CHANGE**)

```bash
sudo gpasswd -d devops01 sudo
sudo -l -U devops01
```

Membership changes may not apply to already running sessions; review and terminate old sessions if the change is urgent. Check for separate `/etc/sudoers.d/` entries that still grant privileges.

## Limited command privilege with sudoers (**CHANGE — advanced**)

**Example:** Let `devops01` restart or reload Apache, but do not grant unrestricted sudo.

```bash
sudo visudo -f /etc/sudoers.d/devops-apache
```

Inside the editor, add exactly:

```sudoers
# User can run these two specific commands as root
# Confirm the path with: command -v systemctl
devops01 ALL=(root) /usr/bin/systemctl restart apache2, /usr/bin/systemctl reload apache2
```

After saving:

```bash
sudo chmod 440 /etc/sudoers.d/devops-apache
sudo visudo -c
sudo -l -U devops01
```

From the user's new session, test:

```bash
sudo systemctl restart apache2
sudo -l
```

**Warning:** Restrict the exact command and arguments; avoid `NOPASSWD: ALL`, interactive shells, general editors, or commands that enable arbitrary file writes. Even narrow sudo rules require a security review. If the user is also in `sudo`, the narrow rule does **not** remove their broad permissions.

## Remove a limited sudo rule (**CHANGE**)

```bash
sudo rm /etc/sudoers.d/devops-apache  # Only the approved custom rule file
sudo visudo -c
sudo -l -U devops01
```

Never edit sudoers files using careless overwrite commands. Always retain one known-working administrative session when making policy changes.
