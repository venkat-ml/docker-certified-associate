
######seccomp

Secure computing mode (seccomp) is a Linux kernel feature. You can use it to restrict the actions available within the container. The seccomp() system call operates on the seccomp state of the calling process. You can use this feature to restrict your application’s access.

https://docs.docker.com/engine/security/seccomp/


Seccomp (short for Secure Computing Mode) is a security feature of the Linux kernel, used to restrict the syscalls available to a given process. This facility has been in the kernel in various forms since 2.6.12 and has been available in Docker Engine since 1.10. The current implementation in Docker Engine provides a default set of restricted syscalls and also allows syscalls to be filtered via either a whitelist or a blacklist on a per-container basis (i.e. different filters can be applied to different containers running in the same Engine). Seccomp profiles are applied at container creation time and cannot be altered for running containers.



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