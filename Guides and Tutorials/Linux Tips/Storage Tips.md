##  1. Vacuum Old Journal Logs
> Tells systemd's journal log manager to shrink the stored logs down to a maximum of 500MB by deleting the oldest log files first until the total size is under that threshold.
```sh
sudo journalctl --vacuum-size=500M
```

## Mounting a volume to a linux vm (virt)

### For a read/write share, grant the QEMU service account access
```bash
sudo setfacl -m u:qemu:x \
    /home/nethunranasingha \
    /home/nethunranasingha/Downloads

  sudo setfacl -R -m u:qemu:rwX \
    /home/nethunranasingha/Downloads/Shared

  sudo setfacl -R -d -m u:qemu:rwX \
    /home/nethunranasingha/Downloads/Shared

  Then apply the appropriate persistent SELinux label:

  sudo semanage fcontext -a -t svirt_image_t \
    '/home/nethunranasingha/Downloads/Shared(/.*)?'

  sudo restorecon -Rv \
    /home/nethunranasingha/Downloads/Shared
```

### Inside VM
```bash
sudo mkdir -p /mnt/shared
sudo mount -t 9p -o trans=virtio,version=9p2000.L Shared /mnt/shared

#To mount automatically at boot, add this to /etc/fstab inside the guest:
Shared  /mnt/shared  9p  trans=virtio,version=9p2000.L,rw,nofail  0  0

#If mounting reports an unknown filesystem, load the guest’s 9P modules:

sudo modprobe 9p
sudo modprobe 9pnet_virtio
```