# 05 — SSH user access: from laptop to Ubuntu server

[← Home](../README.md) · [← Sudo](04-sudo-administration.md) · [Next: Troubleshooting →](06-troubleshooting-and-auditing.md)

**Goal:** Each team member logs into the Ubuntu server using **their own Linux username and SSH key**. Never share the private key of the default `ubuntu`/admin account.

## Picture the flow

```text
Employee laptop                      Ubuntu / AWS EC2 server
------------------                   ----------------------
PRIVATE key (secret, local)          /home/devops01/.ssh/authorized_keys
PUBLIC key (.pub, shareable) ------> contains the employee's PUBLIC key
ssh -i private_key user@SERVER_IP --> SSH verifies key and account privileges
                                     sudo permission is a SEPARATE decision
```

### Step 1 — Employee generates a key (**EMPLOYEE LAPTOP**)

On Linux/macOS or a suitable OpenSSH terminal, run:

```bash
ssh-keygen -t ed25519 -a 100 -f ~/.ssh/id_ed25519_company -C "devops01-company"
chmod 600 ~/.ssh/id_ed25519_company
cat ~/.ssh/id_ed25519_company.pub  # Give ONLY this output to an admin
```

Use a strong passphrase when prompted. The file without `.pub` is the **PRIVATE** key and must never be emailed, posted in a ticket or uploaded to the server. If the company manages keys centrally, follow its standard issuance process instead.

### Step 2 — Admin creates the Linux user (**SERVER**)

```bash
sudo adduser devops01
id devops01
```

Choose whether the role needs `sudo` or a project group. See [sudo](04-sudo-administration.md) and [group management](02-group-management.md); do not give everybody sudo by default.

### Step 3 — Admin installs the PUBLIC key (**SERVER**)

```bash
sudo install -d -m 700 -o devops01 -g devops01 /home/devops01/.ssh
sudo touch /home/devops01/.ssh/authorized_keys
sudo chown devops01:devops01 /home/devops01/.ssh/authorized_keys
sudo chmod 600 /home/devops01/.ssh/authorized_keys
sudo nano /home/devops01/.ssh/authorized_keys
```

Paste the **one complete public-key line** starting `ssh-ed25519 ...`; save. **Add** it to existing lines; do not erase any approved keys. Then check:

```bash
sudo ls -ld /home/devops01 /home/devops01/.ssh
sudo ls -l /home/devops01/.ssh/authorized_keys
sudo sshd -t
```

Typical home permissions can be `750` or `700`, and the home directory must not be writable by other users/groups. A public key can also be installed with `ssh-copy-id -i ~/.ssh/id_ed25519_company.pub devops01@203.0.113.10` **from the laptop**, but that requires a working existing authentication method (often password login), which may be disabled on cloud images.

### Step 4 — Employee tests access (**EMPLOYEE LAPTOP**)

```bash
ssh -i ~/.ssh/id_ed25519_company devops01@203.0.113.10
whoami
id
sudo -l                 # Only if sudo access was intended
```

If key authentication fails:

```bash
ssh -vvv -i ~/.ssh/id_ed25519_company devops01@203.0.113.10
```

Keep the original administrator session running until the new SSH login and intended access both work.

### Step 5 — Check server-side SSH and cloud network rules (**SERVER / CLOUD CONSOLE**)

```bash
sudo systemctl status ssh --no-pager
sudo sshd -t
sudo sshd -T | grep -Ei '^(passwordauthentication|pubkeyauthentication|permitrootlogin|kbdinteractiveauthentication|allowusers|allowgroups) '
sudo ss -lntp | grep ':22'
sudo ufw status verbose
```

**AWS EC2:** Security Group must allow TCP port 22 from the authorized admin IP/CIDR (ideally a restricted source). AWS NACL and routing also matter. A local Ubuntu UFW rule cannot override a blocking AWS Security Group. For other cloud providers, review their cloud firewall too.

### Step 6 — Optionally harden SSH after the key succeeds (**SERVER; approved maintenance**) 

Use the SSH server configuration policy your organization already has. A common key-only policy is:

```text
PubkeyAuthentication yes
PasswordAuthentication no
KbdInteractiveAuthentication no
PermitRootLogin no
```

**Do not blindly paste this into the bottom of `/etc/ssh/sshd_config`.** Ubuntu commonly includes `/etc/ssh/sshd_config.d/*.conf` near the top, and the first value for most directives wins; cloud-init can also manage a drop-in. Inspect current files and effective values first:

```bash
sudo grep -RniE '^[[:space:]]*(Include|PubkeyAuthentication|PasswordAuthentication|KbdInteractiveAuthentication|PermitRootLogin|AllowUsers|AllowGroups)' /etc/ssh/sshd_config /etc/ssh/sshd_config.d/
sudo sshd -T | grep -Ei '^(pubkeyauthentication|passwordauthentication|kbdinteractiveauthentication|permitrootlogin) '
```

To apply an approved configuration, use `sudoedit` or the team's config management; **preserve and review existing earlier drop-ins**. Then:

```bash
sudo sshd -t                        # Required: syntax check before reload
sudo systemctl reload ssh          # Prefer reload to disruptive restart
sudo sshd -T | grep -Ei '^(pubkeyauthentication|passwordauthentication|kbdinteractiveauthentication|permitrootlogin) '
```

**Critical:** Try a NEW SSH session from another terminal before closing the original. `sshd -t` detects syntax problems, not authentication/authorization failures. Avoid modifying SSH port or `AllowUsers`/`AllowGroups` without a recovery path. On some Ubuntu versions `ssh.socket` participates in listening; port changes require version-specific handling.

### Step 7 — Remove one individual key (**SERVER, approved**) 

```bash
sudo nano /home/devops01/.ssh/authorized_keys
```

Delete only the correct public key line. Save, then try a fresh login test to confirm the revoked key no longer works. This **does not** necessarily end already open sessions. Also check centrally managed authorized keys and external access providers if used.

## Optional: Simple client alias on the employee laptop

Inside `~/.ssh/config` on the **laptop**:

```sshconfig
Host company-ec2
    HostName 203.0.113.10
    User devops01
    IdentityFile ~/.ssh/id_ed25519_company
    IdentitiesOnly yes
```

Then connect using `ssh company-ec2`. Restrict local private-key permissions with `chmod 600 ~/.ssh/id_ed25519_company`.
