# **Submodules**

1. Add Submodule
```git
git add submodule <SUBMODULE_URL>
```

2. Update Submodule
```git
git submodule update --init --recursive

git submodule update --recursive --remote
```

3. Remove Cached File/Folder
```git
git rm -r --cached my-folder/
```

4. Tag Commit
```git
git tag -a v1.0.0 -m "Release version 1.0.0"
```
```git
git push origin main      # Push the commit
git push origin v1.0.0    # Push the tag
```
