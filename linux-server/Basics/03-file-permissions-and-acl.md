# 03 — File & directory permissions, ownership and ACLs

[← Home](../README.md) · [← Groups](02-group-management.md) · [Next: Sudo →](04-sudo-administration.md)

## Understand `ls -l`

```text
-rw-r----- 1 devops01 app-team 1024 Nov 17 12:00 config.ini
│└┬┘└┬┘└┬┘    owner   group
│ │  │  └─ others: ---  (no access)
│ │  └──── group: r--   (read only)
│ └─────── owner: rw-   (read/write)
└───────── - = regular file; d = directory
```

| Letter | On a file | On a directory | Value |
|---|---|---|---|
| `r` | Read contents | List names (subject to traversal) | 4 |
| `w` | Edit contents | Create/delete/rename entries with `x` | 2 |
| `x` | Run executable | Traverse/enter directory | 1 |

Permissions are expressed as **owner / group / others**. For example `750 = rwx r-x ---` (7, 5, 0).

## Check current access (**READ**)

```bash
ls -l /srv/company-app
ls -ld /srv/company-app             # The DIRECTORY itself
stat /srv/company-app              # Owner, group, numeric mode
namei -l /srv/company-app/config.ini  # Permissions at each parent directory
id devops01
```

## Set permissions (**CHANGE**)

```bash
sudo chmod 640 /srv/company-app/config.ini   # Owner rw, group r, others none
sudo chmod 750 /srv/company-app              # Owner rwx, group rx, others none
sudo chmod u+x /srv/company-app/deploy.sh    # Add execute for owner
sudo chmod g-w /srv/company-app/config.ini   # Remove group write
```

**Do not use `chmod 777` to solve an application error.** Find the actual user running the service, the owning group, and the minimum privileges required.

## Change ownership (**CHANGE**)

```bash
sudo chown devops01:app-team /srv/company-app/config.ini
sudo chgrp app-team /srv/company-app/config.ini
```

`chown -R` applies recursively and is risky. Do not apply it to `/`, `/etc`, `/var/lib/mysql`, system directories, or an entire production app without reviewing exact targets and effects.

## Shared project folder (group collaboration)

```bash
sudo groupadd app-team                        # Skip if it already exists
sudo usermod -aG app-team devops01
sudo usermod -aG app-team developer02         # Requires user developer02 to exist
sudo install -d -o root -g app-team -m 2770 /srv/company-app
ls -ld /srv/company-app
```

`2770`: owner `rwx`, group `rwx`, others `---`, plus **setgid** on the directory, so new entries generally inherit its group. The creating process's `umask` may still block group write. Test creating/editing files with a newly logged-in group member. Avoid moving an entire production app into this model without reviewing its service account.

## Understand umask

```bash
umask             # Show the current default permission mask
umask 0027        # For THIS shell: generally files 640, directories 750
```

The result depends on the mode requested when a file/directory is created; this is not a fix for permissions on existing files.

## Use ACLs when ordinary owner/group/others is not enough

```bash
sudo apt-get install acl                    # If setfacl/getfacl are unavailable
getfacl /srv/company-app
sudo setfacl -m u:devops01:r-x /srv/company-app  # Named user's access to folder
sudo setfacl -m d:u:devops01:r-x /srv/company-app  # Defaults for NEW entries
getfacl /srv/company-app
sudo setfacl -x u:devops01 /srv/company-app # Remove the named access ACL
sudo setfacl -x d:u:devops01 /srv/company-app # Remove named default ACL
```

A default ACL affects **newly created entries**, not existing children. ACL **mask**, parent directory permissions, and the creating application's requested file mode can limit effective access. Use `getfacl` to check the real result.

## Common safe modes

| Path | Often suitable | Why |
|---|---|---|
| Public non-secret file | `644` | Owner edits; others can read |
| Private config/secrets file | `600` or `640` | Only owner or explicitly approved group |
| Private directory | `700` | Only owner enters |
| Team read-only directory | `750` | Owner full; group read/traverse |
| Shared team write directory | `2770` | Group write + group inheritance |
| `~/.ssh` | `700` | SSH directory must be private |
| `~/.ssh/authorized_keys` | `600` | Authorized key list must not be writable by others |

**Reminder:** File access and directory traversal work together. A file can be world-readable but inaccessible if a parent directory denies `x`.
