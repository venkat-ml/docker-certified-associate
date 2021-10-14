

```bash
$ docker run --dns 10.0.0.2 busybox nslookup google.com
```


or edit your /etc/docker/daemon.json to have something like:

```json
{
    "dns": ["10.0.0.2", "8.8.8.8"]
}
```

then restart docker service  

```bash
$ sudo systemctl docker restart
```