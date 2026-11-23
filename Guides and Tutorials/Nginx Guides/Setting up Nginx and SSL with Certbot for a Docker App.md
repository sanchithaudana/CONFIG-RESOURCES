
This guide walks you through setting up Nginx as a reverse proxy for an app running in Docker (e.g., Outline), and securing it with a Let's Encrypt SSL certificate using Certbot.


1. Create an Nginx Configuration File

Create a new file in `/etc/nginx/sites-available/`:

    sudo nano /etc/nginx/sites-available/outline

Paste the following into the file (change domain and port as needed):

    server {
        listen 80;
        server_name wiki.minthrm.com;

        location / {
            proxy_pass http://localhost:3000/;

            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "Upgrade";
            proxy_set_header Host $host;

            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Scheme $scheme;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_redirect off;
        }
    }

---

## 2. Create a Symbolic Link to Enable the Site

    sudo ln -s /etc/nginx/sites-available/outline /etc/nginx/sites-enabled/

---

## 3. Apply and Test the Nginx Configuration

Test for syntax errors:

    sudo nginx -t

Reload Nginx:

    sudo systemctl reload nginx

---

## 4. Install Certbot and the Nginx Plugin

For Ubuntu/Debian:

    sudo apt update
    sudo apt install certbot python3-certbot-nginx

---

## 5. Generate and Install an SSL Certificate

Run Certbot with the Nginx plugin:

    sudo certbot --nginx -d wiki.minthrm.com

Choose the option to redirect HTTP to HTTPS when prompted.

Certbot will:
- Fetch the certificate
- Update the Nginx config to use HTTPS
- Reload Nginx

To test auto-renewal:

    sudo certbot renew --dry-run