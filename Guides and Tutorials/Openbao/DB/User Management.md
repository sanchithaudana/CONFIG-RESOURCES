## Export Creds
```bash
export BAO_ADDR="https://vault.minchy.ai"
```
```bash
export BAO_TOKEN="<TOKEN>"
```

## List created users
```bash
bao list -namespace=<Namespace> sys/leases/lookup/database/creds/<ROLE>
```

## Lookup credentials
```bash
bao read -namespace=<Namespace> database/creds/<ROLE>
```

## Check Lease
```bash
bao lease lookup -namespace=<Namespace> "<lease_id_from_above>"
```

