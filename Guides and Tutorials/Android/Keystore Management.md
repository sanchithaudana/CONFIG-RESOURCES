## Generate a release keystore
```bash
 keytool -genkey -v -keystore release.keystore -keyalg RSA -keysize 2048 -validity 10000 -alias <ALIAS>
```

## Base64 encode it
```bash
base64 -w 0 release.keystore > release.keystore.b64
```
