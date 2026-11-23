## Read-Only

### Create
```bash
CREATE USER '{{name}}'@'%' IDENTIFIED BY '{{password}}';
```
```bash
GRANT SELECT ON `<DB_NAME>`.* TO '{{name}}'@'%';
```

### Revoke
```bash
DROP USER '{{name}}'@'%';
```

## Read-Write

### Create
```bash
CREATE USER '{{name}}'@'%' IDENTIFIED BY '{{password}}';
```
```bash
GRANT SELECT, INSERT, UPDATE, DELETE ON `<DB_NAME>`.* TO '{{name}}'@'%';
```

### Revoke
```bash
DROP USER '{{name}}'@'%';
```

