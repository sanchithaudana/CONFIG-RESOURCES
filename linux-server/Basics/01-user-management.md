# 01 — User management

[← Home](../README.md) · [← Basics](00-basics-and-safety.md) · [Next: Groups →](02-group-management.md)

> Use one account per employee. Examples use `devops01` — replace that everywhere with the approved username.

## List and inspect users (**READ**)

```bash
whoami                                  # My current username
id                                      # My UID, GID and groups
id devops01                             # Inspect one user
getent passwd devops01                  # Confirm the account exists
getent passwd | cut -d: -f1            # Names from the system account database
who                                     # Currently logged-in sessions
w                                       # Logged-in users and their processes
last -n 10                              # Recent logins (where login history is available)
```

**Note:** `getent passwd` includes system/service accounts; not all entries are human users. If your organization uses LDAP/SSSD, `getent` can return externally managed identities too.

## Create a regular employee (**CHANGE**)

```bash
sudo adduser devops01                    # Guided setup: home, account, password
id devops01                             # Verify account
ls -ld /home/devops01                   # Verify home folder
```

`adduser` on Ubuntu is simpler for humans than low-level `useradd`. A username does not automatically have SSH key access or sudo privileges.

For a strictly SSH-key-managed account, an administrator can instead use `sudo adduser --disabled-password devops01` and install the public key; verify login before removing any fallback admin access.

## Change, lock, or expire an account

```bash
sudo passwd devops01                     # Set/reset account password
sudo passwd -S devops01                  # Read password status
sudo passwd -l devops01                  # Lock PASSWORD authentication only
sudo passwd -u devops01                  # Unlock password if policy permits
sudo chage -l devops01                   # Display password/account aging
sudo chage -E 2027-01-31 devops01        # Set a known approved expiry date
sudo chage -E -1 devops01                # Remove an expiry if authorized
```

**Important:** `passwd -l` is **not a complete SSH access revocation**: an existing authorized SSH key may still work. To revoke the account, see [Offboarding](07-production-scenarios.md#scenario-4--employee-leaves-the-company).

## Change a username or shell (plan carefully)

```bash
sudo usermod -s /bin/bash devops01        # Interactive bash shell
sudo usermod -s /usr/sbin/nologin devops01  # Deny normal interactive shells
```

Changing a live username/home path can break service ownership, cron jobs and deployments. Handle it as an approved migration, not a routine one-liner.

## Delete a user (**DISRUPTIVE**)

```bash
sudo userdel devops01                    # Deletes account; leaves home files
sudo userdel -r devops01                 # Deletes account + home/mail (DATA LOSS)
```

Do this only **after** reviewing running processes, key access, scheduled tasks, ownership and the retention/backup requirement. Prefer disabling access first, then deleting after the approved retention period.

## View the account file formats (**READ**)

```bash
getent passwd devops01
sudo passwd -S devops01
getent group sudo
```

`/etc/passwd` contains account metadata, `/etc/group` stores group metadata, and `/etc/shadow` stores sensitive password hashes and expiry values. Avoid exposing `/etc/shadow` in tickets or terminal screenshots.
