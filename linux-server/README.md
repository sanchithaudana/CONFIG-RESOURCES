# Ubuntu Linux: User, Group, Permissions & SSH Access Runbook

**Purpose:** A reusable, copy-friendly DevOps reference for day-to-day administration of Ubuntu servers (cloud VPS / AWS EC2 / office servers).

**Audience:** Junior/Associate DevOps engineers and other authorized server administrators.

**Scope:** Ubuntu 22.04/24.04 LTS and similar Ubuntu servers. Some commands vary on other Linux distributions.

> **Production safety:** Run commands only on systems you are authorized to manage. Replace sample names, paths and IP addresses before use. Keep an existing working admin SSH session open until your new login and sudo access are tested. Never paste a private SSH key into a server's `authorized_keys` file.

## Start here

| I need to… | Read |
|---|---|
| Learn the basic meanings and symbols | [00 — Basics and safety](00-basics-and-safety.md) |
| Create a user, reset passwords, list accounts | [01 — User management](01-user-management.md) |
| Make teams and assign memberships | [02 — Group management](02-group-management.md) |
| Control read, write and execute access | [03 — File and folder permissions](03-file-permissions-and-acl.md) |
| Give/restrict administrative permissions | [04 — Sudo access](04-sudo-administration.md) |
| Give an employee SSH access using their own key | [05 — SSH user access](05-ssh-access.md) |
| Find why a login or file operation failed | [06 — Troubleshooting and auditing](06-troubleshooting-and-auditing.md) |
| Add, change, or remove access safely | [07 — Production scenarios](07-production-scenarios.md) |
| See commands at a glance | [08 — Command cheat sheet](08-command-cheat-sheet.md) |
| Track onboarding and offboarding | [Onboarding checklist](templates/onboarding-checklist.md) · [Offboarding checklist](templates/offboarding-checklist.md) |

## Typical company workflow

1. Receive approved access request: *who*, *which server*, *what role*, *duration*, *public SSH key*, *approver*.
2. Create an **individual account**, not a shared admin identity.
3. Add only necessary Linux groups and sudo privileges.
4. Add that person's **public** SSH key and set correct permissions.
5. Validate SSH syntax, test login from their laptop, test the exact permitted commands.
6. Document the changes; review and remove access when no longer needed.

## Example names used throughout

| Placeholder | Meaning |
|---|---|
| `devops01` | Sample individual Linux username |
| `app-team` | Sample project team group |
| `203.0.113.10` | **Documentation-only** example server IP; use your actual server IP/DNS |
| `/srv/company-app` | Sample company application directory |
| `~/.ssh/id_ed25519_company` | SSH private key on the **employee's laptop**, never uploaded to the server |
| `~/.ssh/id_ed25519_company.pub` | Matching **public key**, permitted in `authorized_keys` |

## Safety rules worth memorizing

- **Never** run `chmod -R 777 /`, `chown -R ... /`, or recursively change permission on system or database directories.
- **Never** edit `/etc/sudoers` casually; use `visudo` and validate with `visudo -c`.
- **Never** disable SSH password authentication or change `AllowUsers`/`AllowGroups` until a separate SSH session with the new key has succeeded.
- **Never** replace `authorized_keys` with `>` when adding another key: append or edit carefully.
- **Never** use `usermod -G group user` alone to add a supplementary group; it overwrites other supplementary groups. Use `-aG`.
- **Never** delete an account's home directory without an approved backup and retention decision.
- An EC2 security group, cloud firewall, and Ubuntu firewall can all influence whether SSH is reachable.

## Official references

- [Ubuntu — User management](https://ubuntu.com/server/docs/how-to/security/user-management/)
- [Ubuntu — OpenSSH server](https://ubuntu.com/server/docs/how-to/security/openssh-server/)
- [Ubuntu — File access / ACL concepts](https://ubuntu.com/server/docs/share-access-controls/)

**Runbook revision:** 2026-11-17 · **Review before production changes** · This guide is a template, not an automatic change script.
