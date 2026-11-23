

# 20.04  never update

```

cat /etc/os-release

```


```
sudo apt-get -s install --only-upgrade \
  php7.4-common='1:7.4.33-37+ubuntu24.04.1+deb.sury.org+1' \
  php7.4-cli='1:7.4.33-37+ubuntu24.04.1+deb.sury.org+1' \
  php7.4-fpm='1:7.4.33-37+ubuntu24.04.1+deb.sury.org+1' \
  libapache2-mod-php7.4='1:7.4.33-37+ubuntu24.04.1+deb.sury.org+1'
  
sudo apt-get install --only-upgrade \
  php7.4-common='1:7.4.33-37+ubuntu24.04.1+deb.sury.org+1' \
  php7.4-cli='1:7.4.33-37+ubuntu24.04.1+deb.sury.org+1' \
  php7.4-fpm='1:7.4.33-37+ubuntu24.04.1+deb.sury.org+1' \
  libapache2-mod-php7.4='1:7.4.33-37+ubuntu24.04.1+deb.sury.org+1'
  
sudo systemctl restart php7.4-fpm
sudo systemctl restart apache2

dpkg-query -W -f='${Package} ${Version}\n' \
  php7.4-common php7.4-cli php7.4-fpm libapache2-mod-php7.4

systemctl is-active php7.4-fpm apache2
```