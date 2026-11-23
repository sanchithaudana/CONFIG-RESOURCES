
## Step 1: Install `apache2-utils` for htpasswd
```bash
sudo apt install apache2-utils -y
```

## Step 2: Create a password file for Nginx
```bash
sudo htpasswd -c /etc/nginx/.htpasswd jaegeruser
```

## Step 3: Configure Nginx Reverse Proxy

`/etc/nginx/sites-available/jaeger.conf`

```nginx
server {
    listen 8443 ssl;
    server_name _;  # IP based, no domain

    ssl_certificate     /etc/ssl/certs/selfsigned.crt;
    ssl_certificate_key /etc/ssl/private/selfsigned.key;

    location / {
        proxy_pass         http://127.0.0.1:16686;
        proxy_set_header   Host $host;
        proxy_set_header   X-Real-IP $remote_addr;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;

        # Basic Authentication
        auth_basic           "Restricted Access";
        auth_basic_user_file /etc/nginx/.htpasswd;
    }
}
```

## Step 4: Generate a Self-Signed SSL Cert
```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/ssl/private/selfsigned.key \
  -out /etc/ssl/certs/selfsigned.crt
```

## Step 5: Enable and Restart Nginx
```bash
sudo ln -s /etc/nginx/sites-available/jaeger.conf /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```