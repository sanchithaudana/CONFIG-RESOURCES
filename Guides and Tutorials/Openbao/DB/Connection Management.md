## Connection URL
```bash
{{username}}:{{password}}@tcp(<MYSQL_HOST>:<PORT>)/
```

## Username Template
```bash
{{ printf "v-%s-%s" (.RoleName | truncate 8) (random 20) | truncate 32 }}
```
