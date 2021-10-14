**Introduction**

Storage volumes provide a powerful and flexible way to add persistent storage to your containers, but what if you need to share storage volumes across multiple Docker hosts, such as a Swarm cluster? In this lab, you will have the opportunity to work with a simple method of creating shared volumes usable across multiple swarm nodes using the sshfs volume driver.

**Solution**

1.Begin by logging in to the storage server using the credentials provided on the hands-on lab page:

```
ssh cloud_user@PUBLIC_IP_ADDRESS
```

**Set up the External Storage Location**

1.On the storage server, create the storage directory.

```
sudo mkdir -p /etc/docker/storage
sudo chown cloud_user:cloud_user /etc/docker/storage
```

2.Copy the nginx configuration file into the storage directory.

```
cp /home/cloud_user/nginx.conf /etc/docker/storage/
```
**Install the vieux/sshfs Plugin**

1.Install the vieux/sshfs plugin on the swarm manager and worker node.

```
docker plugin install --grant-all-permissions vieux/sshfs
```
**Create the nginx Service That Uses the Shared Volume**

1.Create the nginx-web service on the swarm manager.

Create the container. Be sure to replace <cloud_user password> with the actual password.

```
docker service create -d \
   --replicas=3 \
   --name nginx-web \
   -p 8080:9773 \
   --mount volume-driver=vieux/sshfs,source=nginx-config-vol,target=/etc/nginx/,volume-opt=sshcmd=cloud_user@10.0.1.103:/etc/docker/storage,volume-opt=password="<cloud_user password>" nginx:latest
```

2.Verify that the service is working properly.

```
curl localhost:8080
```
If everything is set up correctly, you should see HTML from the nginx Welcome page.