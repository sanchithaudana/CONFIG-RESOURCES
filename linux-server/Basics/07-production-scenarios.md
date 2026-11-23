# 07 — Real company DevOps scenarios

[← Home](../README.md) · [← Troubleshooting](06-troubleshooting-and-auditing.md) · [Next: Cheat sheet →](08-command-cheat-sheet.md)

These are **manual runbooks**, not an instruction to perform all steps automatically. Confirm change approval, replace sample values and evaluate role scope first.

## Scenario 1 — New DevOps engineer requires SSH and full sudo

**Approval required:** employee name, server, authorized laptop public key, full sudo justification, expiry/review date.

On the **server** as an existing admin:

```bash
sudo adduser devops01
sudo usermod -aG sudo devops01
sudo install -d -m 700 -o devops01 -g devops01 /home/devops01/.ssh
sudo touch /home/devops01/.ssh/authorized_keys
sudo chown devops01:devops01 /home/devops01/.ssh/authorized_keys
sudo chmod 600 /home/devops01/.ssh/authorized_keys
sudo nano /home/devops01/.ssh/authorized_keys   # Paste public key, keep existing lines
sudo sshd -t
id devops01
sudo -l -U devops01
```

On the **new employee's laptop**:

```bash
ssh -i ~/.ssh/id_ed25519_company devops01@203.0.113.10
whoami
sudo -l
sudo whoami
```

**Done only when:** SSH works with the individual key, sudo works as approved, old admin session remains working, and change is documented.

## Scenario 2 — Developer should access project files, NOT sudo

On the server:

```bash
sudo adduser developer02
sudo groupadd app-team                       # Only if not already present
sudo usermod -aG app-team developer02
sudo install -d -o root -g app-team -m 2750 /srv/company-app
id developer02
```

Add the developer's public SSH key exactly as in [SSH Step 3](05-ssh-access.md#step-3--admin-installs-the-public-key-server) using the correct username and home path. `2750` gives the group read/traverse on the directory, no group write. Actual files also need readable group permissions and all parent directories must be traversable. Do not accidentally replace an existing production directory or its ownership. If `/srv/company-app` already exists, inspect it before using `install -d`.

Validate in a fresh login:

```bash
id
sudo -l                        # Should not show unrestricted admin access
ls -la /srv/company-app
```

## Scenario 3 — Several team members need to edit a shared workspace

```bash
sudo groupadd app-team                       # If not present
sudo usermod -aG app-team devops01
sudo usermod -aG app-team developer02
sudo install -d -o root -g app-team -m 2770 /srv/team-workspace
ls -ld /srv/team-workspace
```

**Expected:** members can enter/create/edit according to directory and file modes. New entries inherit group due to setgid, but group-write for files is influenced by `umask`. Test with both newly logged-in users. For reliable multi-user write behavior, review default ACLs and application modes with `getfacl`/`setfacl`; don't relax security to `777`.

## Scenario 4 — Employee leaves the company

**Goal:** revoke access quickly while preserving company data and audit history. Coordinate with HR/change owner and check all servers, central identities, cloud accounts, deployment keys and secrets.

```bash
id devops01
sudo -l -U devops01
sudo passwd -l devops01                # Password lock ONLY; key login can persist
sudo chage -E 0 devops01              # Expire account to block new account logins
sudo gpasswd -d devops01 sudo         # If member; review other sudo rules too
sudo nano /home/devops01/.ssh/authorized_keys # Remove approved public-key line(s)
```

**Urgent revocation:** existing sessions do not automatically vanish. After ensuring this is not a shared/service account and assessing any running jobs:

```bash
sudo loginctl terminate-user devops01  # Ends that user's systemd login sessions
```

Also check cron, user-owned services, files, authorized keys managed elsewhere, and access to GitLab/GitHub/cloud control planes. Expiring an account can affect legitimate jobs running under that identity, so use the approved incident/change process. Retain account and home until retention approval.

**Later, after data retention is approved (irreversible):**

```bash
sudo userdel -r devops01
```

## Scenario 5 — Let an operator restart Apache without full sudo

Check current identity and Apache service, then configure a **specific** sudo rule, described fully in [04 — Sudo](04-sudo-administration.md#limited-command-privilege-with-sudoers-change--advanced). For non-admin project access, prefer Linux groups or delegated service management rather than broad sudo.

## Scenario 6 — User gets `Permission denied (publickey)`

1. Confirm **username**, actual server IP, chosen private key and matching public key.
2. On server: `sudo ls -ld /home/devops01 /home/devops01/.ssh` and `sudo ls -l /home/devops01/.ssh/authorized_keys`.
3. Ensure `.ssh` is owned by account and mode `700`; `authorized_keys` owned by account and mode `600`; home is not group/world-writable.
4. Check `sudo journalctl -u ssh --since "30 minutes ago"`, and `sudo sshd -T` for key settings.
5. Keep existing admin session open, retry `ssh -vvv -i ... user@server` from laptop, then document the outcome.

## Scenario 7 — EC2 server connection is blocked by port 22

First check **AWS Security Group** inbound TCP 22 from the approved source CIDR, NACL/routing, target instance public IP/EIP, and Ubuntu `ufw` rules. If still blocked and no SSH works, use **AWS console-supported recovery** (e.g., EC2 Serial Console when available/configured, Systems Manager if set up, or a carefully controlled root-volume rescue procedure). Do not assume all recovery methods are enabled; don't detach disks or reboot a production instance without an approved plan.
