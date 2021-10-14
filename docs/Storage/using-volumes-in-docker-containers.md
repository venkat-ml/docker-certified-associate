**Introduction**

Containers are designed to be ephemeral, so when you need persistent data, it is usually not a good idea to store it directly in the container's file system. This is where Docker volumes come into play. Docker volumes allow you to store persistent data outside the container itself, providing greater flexibility in what you can do with your data.

In this lab, you will have the opportunity to solve a complex problem using Docker volumes. You will have a chance to work with both shared volumes and bind mounts in order to implement two containers which work together, one container transforming data created by the other. This will give you some practice in working with volumes, as well as some insight into the many ways in which Docker volumes can be used.

**Solution**

1.Begin by logging in to the lab server using the credentials provided on the hands-on lab page:

```
ssh cloud_user@PUBLIC_IP_ADDRESS
```
**Create the Shared Volume and Counter Container**

1.Create a shared volume.

```
docker volume create test-data
```
2.Create the counter container with the provided command, and mount the shared volume to the container.

```
docker run --name counter -d \
  --mount type=volume,source=test-data,destination=/var/log/test \
  busybox \
  sh -c 'i=0; while true; do echo "$i: $(date)" >> /var/log/test/1.log; i=$((i+1)); sleep 1; done'
```  
You can confirm that the counter container is generating data by examining the file inside the container:

```
docker exec counter cat /var/log/test/1.log
```

**Create the fluentd Container**

1.Create the fluentd container and mount the shared volume, the config file, and the output directory to it.
```
docker run --name fluentd -d \
  --mount type=volume,source=test-data,destination=/var/log/input \
  --mount type=bind,source=/etc/fluentd/fluent.conf,destination=/fluentd/etc/fluent.conf \
  --mount type=bind,source=/etc/fluentd/output,destination=/var/log/output \
  --env FLUENTD_ARGS="-c /fluentd/etc/fluent.conf" \
  k8s.gcr.io/fluentd-gcp:1.30
```  
2.Verify that the fluentd container is generating output on the Docker host.

```
ls /etc/fluentd/output
```

You should see some files containing the transformed log data.