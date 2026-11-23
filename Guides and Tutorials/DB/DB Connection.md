
## Connecting with SSL
### MySQL
```bash
mysql \
  -u <USER> \
  -h <HOST> \
  -P <PORT> \
  --ssl-ca=/path/to/ca.pem \
  -p
```

```bash
mariadb \
  -u <USER> \
  -h <HOST> \
  -P <PORT> \
  --ssl-ca=/path/to/ca.pem \
  -p
```

## Connecting with SSL Disabled
### Mariadb
```bash
mariadb \
  --ssl \
  --ssl-verify-server-cert=0 \
  -u <USER> \
  -h <HOST> \
  -P <PORT> \
  -p
```
