

```bash
$ grep SECCOMP /boot/config-$(uname -r) 
```

Need to have Y for all the flags

```bash
sudo systemctl cat docker
```

```bash
/etc/docker/seccomp.json
```

defaultAction - SCMP_ACT_ERRNO

*my_seccomp_profile.json*
```json
 {
  "defaultAction": "SCMP_ACT_ALLOW",
  "architectures": [
    "SCMP_ARCH_X86_64",
    "SCMP_ARCH_X86",
    "SCMP_ARCH_X32"
  ],
  "syscalls": [
    {
      "name": "chmod",
      "action": "SCMP_ACT_ERRNO"
    },
    {
      "name": "mkdir",
      "action": "SCMP_ACT_ERRNO"
    }
  ]
}
```

```bash
docker run --rm -it --security-opt seccomp=/home/cloud_user/seccomp_stuff/my_seccomp_profile.json debian:jessie sh 
```

```bash
$ mkdir test  # works - it denies
$ chmod +x  test # Doesn't work - chmod is being called differently in Ubuntu
```

```bash
docker run --rm -it --security-opt seccomp=unconfined debian:jessie sh

$ touch /home/test
$ apt-get update
$ apt-get install strace
$ strace -qc chmod +x /home/test

# fchmodat is the actual call here
```

*my_seccomp_profile.json*
```json
 {
  "defaultAction": "SCMP_ACT_ALLOW",
  "architectures": [
    "SCMP_ARCH_X86_64",
    "SCMP_ARCH_X86",
    "SCMP_ARCH_X32"
  ],
  "syscalls": [
    {
      "name": "chmod",
      "action": "SCMP_ACT_ERRNO"
    },
    {
      "name": "fchmod",
      "action": "SCMP_ACT_ERRNO"
    },
    {
      "name": "fchmodat",
      "action": "SCMP_ACT_ERRNO"
    },
    {
      "name": "mkdir",
      "action": "SCMP_ACT_ERRNO"
    }
  ]
}
```

```bash
docker run --rm -it --security-opt seccomp=/home/cloud_user/seccomp_stuff/my_seccomp_profile.json debian:jessie sh 
```

```bash
$ mkdir test  # works - it denies
$ chmod +x  test # works - it denies
```