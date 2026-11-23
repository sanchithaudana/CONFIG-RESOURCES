##  Wireless ADB

### Restart in TCP/IP Mode
```bash
adb tcpip 5555
```

### Connect over WIFI
```bash
adb connect <IP>:5555
```

### Disable Screen Timeout
```bash
adb shell settings put system screen_off_timeout 2147483647
```
#### Verify
```bash
adb shell settings get system screen_off_timeout
```

### Keep Screen On while charging
```bash
adb shell settings put global stay_on_while_plugged_in 7
```
> `1` = AC
> `2` = USB
> `4` = Wireless
> `7` = Any power source
#### Disable
```bash
adb shell settings put global stay_on_while_plugged_in 0
```
