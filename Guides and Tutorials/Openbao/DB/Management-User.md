## MySQL
### Global
```sql
CREATE USER 'openbao'@'%' IDENTIFIED BY 'Zk789P-Dy5heaUmkPybSMxSb7TD-235yTr';
```
```sql
GRANT CREATE USER ON *.* TO 'openbao'@'%';
```
```sql
GRANT SELECT, INSERT, UPDATE, DELETE ON *.* TO 'openbao'@'%';
```
```sql
GRANT GRANT OPTION ON *.* TO 'openbao'@'%';
```
```sql
FLUSH PRIVILEGES;
```

### Scoped
```sql
CREATE USER 'openbao_<CLIENT>'@'%' IDENTIFIED BY 'Zk789P-Dy5heaUmkPybSMxSb7TD-235yTr';
```
```sql
GRANT CREATE USER ON *.* TO 'openbao_<CLIENT>'@'%';
```
```sql
GRANT SELECT, INSERT, UPDATE, DELETE ON `<DB>`.* TO 'openbao_<CLIENT>'@'%' WITH GRANT OPTION;
```
```sql
FLUSH PRIVILEGES;
```
