
Docker automatically generates and loads a default profile for containers named docker-default. The Docker binary generates this profile in tmpfs and then loads it into the kernel.

Note: This profile is used on containers, not on the Docker Daemon.
```
$ docker run --rm -it --security-opt apparmor=docker-default hello-world
```