
## Creating a regcred
```sh
kubectl create secret generic regcred \
    --from-file=.dockerconfigjson=/home/nethunranasingha/.docker/config.json \
    --type=kubernetes.io/dockerconfigjson \
    -n <NAMESPACE>
```

## Updating certificates on a kubeadm cluster

### Check expiration
```bash
sudo kubeadm certs check-expiration
```

### Renew Certs
```bash
sudo kubeadm certs renew all
```

### Restart Server
```bash
sudo reboot now
```