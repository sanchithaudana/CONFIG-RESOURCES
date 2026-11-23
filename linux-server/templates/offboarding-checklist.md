# Server access revocation / offboarding checklist

> Use an approved ticket. Preserve data and logs until company retention policy allows removal.

## Request

- Ticket / approval:
- User and username:
- Server inventory scope:
- Revocation date/time:
- Any service accounts or active critical jobs owned by this identity?:
- Evidence location / retention owner:

## Revocation

- [ ] Identify all server logins and non-local identity providers
- [ ] Inspect user memberships and sudo rules
- [ ] Lock password and expire/disable login according to policy
- [ ] Remove the user's SSH public key(s) / central SSH authorization
- [ ] Remove sudo and project groups as required
- [ ] Revoke cloud console, Git provider, CI/CD, VPN, application and secrets access separately
- [ ] End active sessions when safe and approved
- [ ] Check cron jobs, systemd user services, deployed keys, tokens and owned files
- [ ] Verify a new SSH login using the revoked account/key is denied
- [ ] Record evidence and time of revocation

## Retention and removal

- [ ] Backup/ownership of company files reviewed
- [ ] Retention period and delete approval confirmed
- [ ] Account and home removed **only if** authorized
- [ ] Access inventory/CMDB updated

Completed by / date:
Reviewed by / date:
