## Creating SSH Tunnel-Only Users

Use this to give developers database access via SSH tunnel without giving them shell access.

### 1. Create the user
```sh
sudo useradd -m -s /bin/bash dbdev
```

### 2. Create .ssh directory
```sh
sudo mkdir -p /home/dbdev/.ssh
sudo chmod 700 /home/dbdev/.ssh
sudo touch /home/dbdev/.ssh/authorized_keys
sudo chmod 600 /home/dbdev/.ssh/authorized_keys
sudo chown -R dbdev:dbdev /home/dbdev/.ssh
```

### 3. Generate SSH key pair (developer runs this on their local machine)
```sh
ssh-keygen -t ed25519 -C "dbdev-yourname" -f dbdev-key
```

### 4. Add the public key to authorized_keys on the server
```sh
sudo nano /home/dbdev/.ssh/authorized_keys
```
> Paste the contents of `dbdev-key.pub`

### 5. Restrict user to tunnel-only access
Add to the **end** of `/etc/ssh/sshd_config`:
```sh
sudo nano /etc/ssh/sshd_config
```
```
Match User dbdev
    AllowTcpForwarding yes
    PermitOpen 127.0.0.1:3306
    X11Forwarding no
    AllowAgentForwarding no
    ForceCommand /bin/false
```
> - `PermitOpen 127.0.0.1:3306` — only allows forwarding to MySQL
> - `ForceCommand /bin/false` — blocks shell/command access
> - To allow multiple ports: `PermitOpen 127.0.0.1:3306 127.0.0.1:5432`

### 6. Reload SSH
```sh
sudo systemctl reload ssh
```

### 7. Developer connects with
```sh
ssh -N -L 3306:127.0.0.1:3306 dbdev@your-server-ip
```
Then connect MySQL client to `localhost:3306`.