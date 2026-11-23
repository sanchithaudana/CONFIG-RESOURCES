
> Set zsh as the default terminal
```bash
chsh -s $(which zsh)
```

> Get Memory info
```bash
cat /proc/meminfo
```

> Generate SSH key
```bash
ssh-keygen -t rsa
```

> Add SSH key to remote
```bash
ssh-copy-id -i <KEY> user@host
```

> SCP
```bash
scp <LocalFile> user@remote-server:<RemoteLocation>
```

> Add key to authorized Keys
```bash
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

> Get Running Services and Ports
```bash
netstat -lntu
```

- `-l` = only services which are listening on some port
- `-n` = show port number, don't try to resolve the service name
- `-t` = tcp ports
- `-u` = udp ports
- `-p` = name of the program

	You don't need the 'p' parameter as you're only interested in getting which ports are free and not which program is running on it.

	This only shows which ports on your system are used up, though. This doesn't tell you the status of your network e.g. if you're behind NAT and you want some services to be accessible from outside. Or if the firewall is blocking the port for outside visitors. In that case, nmap comes to the rescue. WARNING: Use nmap only on networks which are under your control. Also, there are firewall rules which can block nmap pings, you'll have to fiddle around with options to get correct results.

> Get Services for ports
```bash
lsof -i:8080
```

>Kill Services for specific Port
```bash
kill -9 $(lsof -t -i:8080)
```

> Reload nginx configs
```bash
/usr/sbin/nginx -s reload
```

> Scroll to bottom of a file using `nano`
```Buttons
CTRL + END
```

> `stdout` to file
```bash
# Add
command > file

# Append
command >> file
```

> `stdout` & `stderr` to file
```bash
# Add
command &> file

# Append
command &>> file
```

> `stdout` & `stderr` displayed and to file
```bash
command 2>&1 | tee file
```

> Connect to WIFI using `nmcli`
```bash
nmcli --ask device wifi connect "SSID"
```

> Flush DNS
```bash
sudo resolvectl flush-caches
```

> Get Linux Capabilities
```bash
/usr/include/linux/capability.h
```

> Network Details about a process
```bash
netstat -anp | grep etcd
```

> Show all the bridge interfaces
```bash
ip address show type bridge
```

> Get first process details
```bash
ps -p 1
```
