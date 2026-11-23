# Server access request / onboarding checklist

> Copy this template into the approved change ticket. Do **not** paste a private key or account password here.

## Request metadata

- Ticket / change ID:
- Requester and approving manager:
- User's name and approved Linux username:
- Server hostname / environment (DEV / UAT / PROD):
- Business reason and access expiry / review date:
- Required project groups:
- Required sudo level: **None / Limited command(s) / Full sudo**
- User's SSH **public key** fingerprint (`ssh-keygen -lf key.pub`):

## Pre-change checks

- [ ] Request approved and scope checked
- [ ] Existing admin session and emergency recovery method available
- [ ] Account name and expected groups confirmed
- [ ] No shared/private keys requested
- [ ] Access matches least-privilege requirement

## Implementation

- [ ] Account created (or existing account inspected)
- [ ] User's approved team groups assigned
- [ ] Sudo level configured separately and justified
- [ ] SSH public key appended to the correct `authorized_keys`
- [ ] SSH paths have correct ownership and permissions
- [ ] `sudo sshd -t` passed (if SSH config changed)
- [ ] No unapproved firewall/port changes made

## Test and sign-off

- [ ] New SSH login successful using user's own key
- [ ] `whoami` and `id` give the intended identity and groups
- [ ] Allowed commands/folder access work
- [ ] Forbidden access (e.g., unrestricted sudo) is denied
- [ ] Event and approval recorded in access register
- [ ] Access review/removal date recorded
- [ ] Existing administrator session left safe and usable

Completed by / date:
Reviewed by / date:
