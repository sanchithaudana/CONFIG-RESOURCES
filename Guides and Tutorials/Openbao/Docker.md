
>Copy from the inside of a container
```bash
docker cp <CONTAINER_NAME>:<FILE_LOCATION> <DESTINATION>
```

> Add capabilities
```bash
docker run --cap-add <CAPABILITY> <IMAGE_NAME>
```
```bash
docker run --cap-add MAC_ADMIN ubuntu
```

> Remove capabilities
```bash
docker run --cap-drop <CAPABILITY> <IMAGE_NAME>
```
```bash
docker run --cap-drop KILL ubuntu
```

> All capabilities
```bash
docker run --privileged <IMAGE_NAME>
```
