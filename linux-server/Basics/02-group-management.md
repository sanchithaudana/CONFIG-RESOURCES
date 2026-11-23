# 02 — Group management

[← Home](../README.md) · [← Users](01-user-management.md) · [Next: Permissions →](03-file-permissions-and-acl.md)

**Why use groups?** If five team members need to edit the same application directory, give the group access once instead of granting permissions five times.

## Inspect groups (**READ**)

```bash
id devops01                        # Includes primary and supplementary groups
groups devops01                    # Readable group list
getent group                       # All available groups
getent group app-team              # One group's details
```

## Create a group (**CHANGE**)

```bash
sudo groupadd app-team
getent group app-team
```

## Add a user to a group (**CHANGE**)

```bash
sudo usermod -aG app-team devops01
id devops01
```

**Always include `-a`** when adding a supplementary group with `-G`. `usermod -G app-team devops01` alone replaces the user's supplementary group list, potentially removing `sudo` or other access.

The user must sign out and sign in again to refresh group membership in existing sessions.

## Remove user from a group (**CHANGE**)

```bash
sudo gpasswd -d devops01 app-team
id devops01
```

## Multiple groups at once (**CHANGE**)

```bash
sudo usermod -aG app-team,developers devops01
```

This assumes both groups exist. It **adds** these memberships; it does not replace other groups.

## Primary group vs supplementary group

- **Primary group:** default group assigned to a newly created file, unless the parent directory has inheritance rules.
- **Supplementary group:** extra groups used to grant access.

```bash
id -gn devops01                       # Primary group name
sudo usermod -g app-team devops01      # Change primary group; plan impact
```

Changing a primary group can alter new-file group ownership. Inspect the user's processes, file ownership and shared directory requirements first.

## Rename/delete a group (**CHANGE / DISRUPTIVE**)

```bash
sudo groupmod -n app-operators app-team
sudo groupdel app-operators
```

Do **not** delete a group that is still required by user accounts or files. Confirm the access impact before running either command.

## Common company team mapping

| Group | Example access |
|---|---|
| `sudo` | Broad administrative access: reserve for trusted admins |
| `app-team` | Shared application files (`/srv/company-app`) |
| `deploy-team` | Approved deployment locations and workflows |
| `www-data` | Common web-service identity/group; **do not** add human users casually |
| `adm` | Potential access to sensitive system logs; grant only if necessary |

Group names alone do **not** grant filesystem or SSH access: the target resources must also reference those groups in permissions or policy.
