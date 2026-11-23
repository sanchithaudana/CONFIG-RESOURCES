# 08 — Quick command cheat sheet

[← Home](../README.md) · [← Scenarios](07-production-scenarios.md)

Replace `devops01`, `app-team`, paths and IP addresses; commands requiring `sudo` modify access or inspect privileged configuration.

| What you want | Command |
|---|---|
| Who am I? | `whoami` |
| My groups | `id` |
| Check one user | `id devops01` |
| List usernames | `getent passwd \| cut -d: -f1` |
| Create user | `sudo adduser devops01` |
| Reset password | `sudo passwd devops01` |
| Lock password only | `sudo passwd -l devops01` |
| Expire account | `sudo chage -E 0 devops01` |
| Create group | `sudo groupadd app-team` |
| Check group | `getent group app-team` |
| Add group membership | `sudo usermod -aG app-team devops01` |
| Remove group membership | `sudo gpasswd -d devops01 app-team` |
| Add broad sudo | `sudo usermod -aG sudo devops01` |
| Check sudo rules | `sudo -l -U devops01` |
| Remove broad sudo group | `sudo gpasswd -d devops01 sudo` |
| Inspect permissions | `ls -ld /srv/company-app` |
| Inspect permission chain | `namei -l /srv/company-app/config.ini` |
| Owner/group of file | `stat /srv/company-app/config.ini` |
| Set owner and group | `sudo chown devops01:app-team FILE` |
| Private file | `sudo chmod 600 FILE` |
| Team read-only file | `sudo chmod 640 FILE` |
| Team-readable folder | `sudo chmod 750 DIR` |
| Shared team folder | `sudo chmod 2770 DIR` |
| Inspect ACL | `getfacl FILE` |
| SSH service | `sudo systemctl status ssh --no-pager` |
| SSH syntax | `sudo sshd -t` |
| Effective SSH config | `sudo sshd -T` |
| Reload SSH config | `sudo systemctl reload ssh` |
| SSH recent log | `sudo journalctl -u ssh --since "1 hour ago"` |
| Firewall | `sudo ufw status verbose` |
| Listening services | `sudo ss -lntp` |
| SSH from laptop | `ssh -i ~/.ssh/id_ed25519_company devops01@203.0.113.10` |
| Debug from laptop | `ssh -vvv -i ~/.ssh/id_ed25519_company devops01@203.0.113.10` |
| Active sessions | `w` |
| Last logins | `last -n 10` |
| End user sessions | `sudo loginctl terminate-user devops01` (**disruptive**) |
| Remove user + files | `sudo userdel -r devops01` (**irreversible**) |

## Golden rules

**SSH account != sudo access != file permission.** They are three separate layers. Always test each layer independently from a new session, apply least privilege, and keep a recovery method ready.
