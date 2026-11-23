## 1. Create the deployment user (no password login, just SSH)
```sh
sudo useradd -m -s /bin/bash deploy
```

## 2. Add user to docker group
```sh
sudo usermod -aG docker deploy
```

## 3. Create .ssh directory for the user
```sh
sudo mkdir -p /home/deploy/.ssh
sudo chmod 700 /home/deploy/.ssh
```

## 4. Generate SSH key pair (run this on your local machine or Jenkins server)
```sh
ssh-keygen -t ed25519 -C "mint-ai-jenkins-deploy" -f mint-ai-jenkins-deploy-key
```

## 5. Add the public key to authorized_keys on the server
```sh
sudo nano /home/deploy/.ssh/authorized_keys
```
> Paste the contents of jenkins-deploy-key.pub

## 6. Set proper permissions
```sh
sudo chmod 600 /home/deploy/.ssh/authorized_keys
sudo chown -R deploy:deploy /home/deploy/.ssh
```

## 7. Verify docker access (as deploy user)
```sh
sudo su - deploy
docker ps
```
