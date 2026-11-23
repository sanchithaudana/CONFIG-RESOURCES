
## Compress
```bash
tar -czvf <name>_$(date +%F).tar.gz <directory>
```

## De-Compress

### Same directory
```bash
tar -xzvf <file>
```

### Different Directory
```bash
tar -xzvf <file> -C <location>
```