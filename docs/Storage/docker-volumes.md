When mounting external storage we can either use bind mount or volumes

**Bind mount:**

- Mount a specific path on the host machine to the container

**Volumes:**

- Stores data on the host file system, but the storage location is managed by docker
- More portable
- Can mount same volume to multiple containers
- Work in many scenarios 


1.Create a directory on the host with some test data.
```bash
cd ~/
mkdir message
echo Hello, world! > message/message.txt
```
2.Mount the directory to a container with a bind mount.
```bash
docker run --mount type=bind,source=/home/cloud_user/message,destination=/root,readonly busybox cat /root/message.txt
```

3.Run a container with a mounted volume.
```bash
docker run --mount type=volume,source=my-volume,destination=/root busybox sh -c 'echo hello > /root/message.txt'
```

4.Use the -v syntax to create a bind mount and a volume.

```bash
docker run -v /home/cloud_user/message:/root:ro busybox cat /root/message.txt
docker run -v my-volume:/root busybox sh -c 'cat /root/message.txt'
```

5.Use a volume to share data between containers.

```bash
docker run --mount type=volume,source=shared-volume,destination=/root busybox sh -c 'echo I wrote this! >
docker run --mount type=volume,source=shared-volume,destination=/anotherplace busybox cat /anotherplace/m
```

6.Create and manage volumes using docker volume commands.
```bash
docker volume create test-volume
docker volume ls
docker volume inspect test-volume
docker volume rm test-volume
```