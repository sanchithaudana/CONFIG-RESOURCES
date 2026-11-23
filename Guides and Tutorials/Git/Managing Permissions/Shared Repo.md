
## Permission Setup

```bash
REPO=/var/www/html/your-repository
OWNER=minthrm
GROUP=developers
```

```bash
# 1. Set the repository owner and group
sudo chown -R "$OWNER:$GROUP" "$REPO"

# 2. Make all directories group-writable and enable setgid
#    New files/directories will inherit the developers group
sudo find "$REPO" -type d -exec chmod 2775 {} +

# 3. Make existing files group-readable and group-writable
#    Existing executable bits are preserved
sudo find "$REPO" -type f -exec chmod g+rw {} +

# 4. Give the developers group access to all existing files/directories
sudo setfacl -R -m "g:$GROUP:rwx,m:rwx" "$REPO"

# 5. Set default ACLs on every directory
#    Newly created files and directories inherit group write access
sudo find "$REPO" -type d \
  -exec setfacl -m \
  "d:u::rwx,d:g::rwx,d:g:$GROUP:rwx,d:m::rwx,d:o::rx" {} +

# 6. Tell Git that this repository is shared by the group
sudo git -C "$REPO" config core.sharedRepository group

# 7. Trust this shared repository system-wide
sudo git config --system --add safe.directory "$REPO"
```