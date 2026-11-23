# 06 — Troubleshooting & auditing access

[← Home](../README.md) · [← SSH](05-ssh-access.md) · [Next: Production scenarios →](07-production-scenarios.md)

## Quick symptom → check → likely fix

| Symptom | Investigate | Common cause / careful resolution |
|---|---|---|
| `Permission denied (publickey)` | `ssh -vvv`, `id`, `ls -ld ~/.ssh`, `authorized_keys`, SSH logs | Wrong username/key, missing public key, bad home/`.ssh` ownership/mode, account restrictions |
| `Connection timed out` | Cloud security group, UFW, route, host IP | Port 22 blocked or wrong reachable address |
| `Connection refused` | `systemctl status ssh`, `ss -lntp` | SSH service not listening on target address/port |
| `user is not in the sudoers file` | `id`, `sudo -l -U user` | No sudo policy, or old session has stale group membership |
| `Operation not permitted` for `chmod`/`chown` | `id`, `stat`, `lsattr` (if supported) | Insufficient privileges, immutable flags, filesystem constraints |
| `Permission denied` opening file | `namei -l PATH`, `getfacl PATH` | File mode, ACL mask or parent-directory traverse access |
| `usermod: group does not exist` | `getent group NAME` | Create or correct group first |
| SSH edits have no effect | `sshd -T`, config `grep`, `ssh.socket` status | Earlier included directives, `Match` rules, cloud-init config or socket activation |

## SSH diagnostics (**READ**)

On the employee laptop:

```bash
ssh -vvv -i ~/.ssh/id_ed25519_company devops01@203.0.113.10
ssh -G company-ec2 | grep -Ei '^(hostname|user|identityfile|port) '
```

On the server:

```bash
sudo systemctl status ssh --no-pager
sudo journalctl -u ssh --since "1 hour ago" --no-pager
sudo sshd -t
sudo sshd -T | grep -Ei '^(passwordauthentication|pubkeyauthentication|permitrootlogin|allowgroups|allowusers) '
getent passwd devops01
id devops01
sudo ls -ld /home/devops01 /home/devops01/.ssh
sudo ls -l /home/devops01/.ssh/authorized_keys
sudo ufw status verbose
sudo ss -lntp | grep ':22'
sudo systemctl status ssh.socket --no-pager  # If socket activation is used
```

**Extra:** If SSH `Match` blocks are used, effective settings can vary by user/source address. For example (replace source address):

```bash
sudo sshd -T -C user=devops01,host=example-client,addr=198.51.100.25
```

Do not print or post private keys or `/etc/shadow`. A public key in a support ticket is less sensitive than a private key, but organizational access records should still be controlled.

## Diagnose file and directory access (**READ**)

```bash
whoami
id
ls -ld /srv /srv/company-app
namei -l /srv/company-app/config.ini
stat /srv/company-app/config.ini
getfacl /srv/company-app/config.ini
```

Remember that `ls -l` on a file alone is **not enough**: the user needs permission to traverse every directory above it.

## Review login and sudo activity (**READ**)

```bash
who
w
last -n 20
sudo journalctl --since "24 hours ago" | grep -Ei 'sudo|sshd|session'
sudo journalctl -u ssh --since "24 hours ago" --no-pager
sudo -l -U devops01
```

Journal format and retention vary by system; `/var/log/auth.log` might be available if rsyslog is configured but isn't guaranteed. For company auditing, prefer central log collection and keep timestamps, approver, change ticket and pre/post configuration evidence.

## Recover from a bad SSH change

1. **Do not close the surviving SSH session.** If locked out, use approved provider console / EC2 recovery and your organization's incident process.
2. Restore the **exact** configuration or drop-in changed; a main config backup alone does not restore modified drop-ins.
3. Validate and reload:

```bash
sudo sshd -t
sudo systemctl reload ssh
sudo systemctl status ssh --no-pager
```

4. Open a new terminal and verify a fresh login. If SSH is still unreachable, inspect firewalls, cloud security groups, socket activation and system journal.
