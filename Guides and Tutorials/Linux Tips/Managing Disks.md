## Viewing Drives and Partitions

### List all block devices

```bash
lsblk
```

### Show detailed disk information

```bash
fdisk -l
```

### Display disk usage

```bash
df -h
```

### Get UUID and filesystem info

```bash
blkid
blkid /dev/sda1
```

## Partitioning a Drive

### Using parted (GPT - recommended for large drives)

```bash
# Create GPT partition table
parted /dev/sda mklabel gpt

# Create a partition using all space
parted /dev/sda mkpart primary ext4 0% 100%

# Create a partition with specific size
parted /dev/sda mkpart primary ext4 0% 500GB
```

### Using fdisk (MBR - for drives <2TB)

```bash
# Start fdisk
fdisk /dev/sda

# Common commands inside fdisk:
# n - create new partition
# d - delete partition
# p - print partition table
# w - write changes and exit
# q - quit without saving
```

## Formatting Partitions

### Format as ext4 (most common)

```bash
mkfs.ext4 /dev/sda1
```

### Format as xfs

```bash
mkfs.xfs /dev/sda1
```

### Format as btrfs

```bash
mkfs.btrfs /dev/sda1
```

### Format as ntfs (for Windows compatibility)

```bash
mkfs.ntfs /dev/sda1
```

## Mounting Drives

### Create mount point

```bash
mkdir -p /mnt/storage
```

### Mount temporarily

```bash
mount /dev/sda1 /mnt/storage
```

### Unmount

```bash
umount /mnt/storage
```

### Mount with specific options

```bash
mount -o rw,noatime /dev/sda1 /mnt/storage
```

## Permanent Mounting (fstab)

### Add to /etc/fstab

```bash
# Get UUID first
blkid /dev/sda1

# Add entry to fstab
echo "UUID=<uuid-here> /mnt/storage ext4 defaults,nofail 0 2" >> /etc/fstab

# Reload systemd and mount
systemctl daemon-reload
mount -a
```

### fstab entry format

```
UUID=<uuid> <mount-point> <filesystem> <options> <dump> <pass>
```

### Common fstab options

- `defaults` - default options (rw, suid, dev, exec, auto, nouser, async)
- `nofail` - don't fail boot if drive is unavailable
- `noatime` - don't update access times (improves performance)
- `ro` - read-only
- `rw` - read-write

## RAID Management

### Check RAID status

```bash
cat /proc/mdstat
```

### Detailed RAID info

```bash
mdadm --detail /dev/md0
```

### Watch RAID sync progress

```bash
watch cat /proc/mdstat
```

## Disk Health and Monitoring

### Check disk health (SMART)

```bash
# Install smartmontools if needed
apt install smartmontools

# Check drive health
smartctl -a /dev/sda

# Run short test
smartctl -t short /dev/sda

# Run long test
smartctl -t long /dev/sda
```

### Check filesystem for errors

```bash
# Unmount first!
umount /dev/sda1

# Check and repair ext4
fsck.ext4 /dev/sda1

# Check and repair xfs
xfs_repair /dev/sda1
```

## Useful Disk Commands

### Check disk I/O performance

```bash
# Install if needed
apt install hdparm

# Test read speed
hdparm -t /dev/sda
```

### View disk usage by directory

```bash
# Current directory
du -sh *

# Specific directory
du -sh /var/log

# Top 10 largest directories
du -h / | sort -rh | head -10
```

### Find large files

```bash
find / -type f -size +1G -exec ls -lh {} \;
```

## Quick Complete Setup Example

```bash
# 1. Partition the drive
parted /dev/sda mklabel gpt
parted /dev/sda mkpart primary ext4 0% 100%

# 2. Format
mkfs.ext4 /dev/sda1

# 3. Create mount point
mkdir -p /mnt/storage

# 4. Get UUID and add to fstab
UUID=$(blkid -s UUID -o value /dev/sda1)
echo "UUID=$UUID /mnt/storage ext4 defaults,nofail 0 2" >> /etc/fstab

# 5. Mount
systemctl daemon-reload
mount -a

# 6. Verify
df -h /mnt/storage
```