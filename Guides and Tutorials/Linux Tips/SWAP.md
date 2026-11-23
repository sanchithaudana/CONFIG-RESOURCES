
## Adding a SWAP Partition

### Others
```bash
fallocate -l 4G /swapfile2
chmod 600 /swapfile2
mkswap /swapfile2
swapon /swapfile2
```

#### Verifying
```bash
swapon --show
free -h
```

#### Making it persistent
```bash
# Add this to `/etc/fstab`
/swapfile2 none swap sw 0 0
```

### Btrfs
```bash
sudo btrfs filesystem mkswapfile --size 10G /swapfile2
sudo swapon /swapfile2
```

#### Verifying
```bash
swapon --show
```

#### Making it persistent
```bash
/swapfile2 none swap defaults 0 0
```