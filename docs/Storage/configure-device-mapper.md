

Device mapper supports 2 modes :

- loop-lvm
- direct-lvm

1. For this lesson, use a CentOS 7 server with a size of Small . Before starting the lesson, you'll first need to install Docker.

2. Add a new storage device to your server. In Playground, select Actions , then select Add /dev/nvme1n1 and wait for it to
finish adding the device.

3. Stop and disable Docker.
```bash
sudo systemctl disable docker
sudo systemctl stop docker
```

4.Delete any existing Docker data.
```bash
sudo rm -rf /var/lib/docker
```

5.Configure DeviceMapper in daemon.json .
```bash
sudo vi /etc/docker/daemon.json
{
"storage-driver": "devicemapper",
"storage-opts": [
"dm.directlvm_device=/dev/nvme1n1",
"dm.thinp_percent=95",
"dm.thinp_metapercent=1",
"dm.thinp_autoextend_threshold=80",
"dm.thinp_autoextend_percent=20",
"dm.directlvm_device_force=true"
]
}
```

6.Start and enable Docker.
```bash
sudo systemctl enable docker
sudo systemctl start docker
```

7.Check the storage driver information provided by docker info .
```bash
docker info
```

8.Run a container to verify that everything is working.
```bash
docker run hello-world
```