## Install Docker in Ubuntu


```bash

sudo apt-get update
sudo apt-get -y install \
 apt-transport-https \
 ca-certificates \
 curl \
 gnupg-agent \
 software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88
sudo add-apt-repository \
  "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) \
  stable"
 
sudo apt-get install -y docker-ce=5:18.09.5~3-0~ubuntu-bionic docker-ce-cli=5:18.09.5~3-0~ubuntu-bionic containerd.io

sudo usermod -a -G docker cloud_user

```

```bash
sudo docker run hello-world
```

Fundementals behind adding cloud_user to docker group

```bash
cat /ectc/passwd
cat /etc/group
sudo usermod -a -G docker cloud_user

/etc/sysconfig/docker   # Docker daemon config options
```

Modify the /etc/sysconfig/docker file 

```bash
OPTIONS='--selinux-enabled --log-driver=journald --signature-verification=false -G docker'
``` 

Restart the docker daemon

```bash
 sudo systemctl daemon-reload
 sudo systemctl restart docker

``` 

Now we can run without using sudo

```bash
docker run hello-world
```

```bash
docker run --rm -it --security-opt seccomp=unconfined --cpus="0.2" --memory="500m" docker.io/python
```

```python
import threading

def test():
    while True:
      1000 * 1000

>>> threading.Thread(target=test).start()
```

Install htop to check the processes, CPU and memory consumption

```bash
yum install htop
```

###### overall limitations can be provided at /etc/system/,y_limits.slice

```bash
Description=my slice for docker resources
before=slices.target

[Slice]
CPUAccounting=true
CPUQuota=20%
MemoryAccounting=true
MemoryLimit=200M
```
```bash
 sudo systemctl daemon-reload
 sudo systemctl restart docker
``` 
```bash
 docker run --rm -it --cgroup-parent=my_limits.slice docker.io/python
``` 
```python
import threading

def test():
    while True:
      1000 * 1000

>>> threading.Thread(target=test).start()
```
