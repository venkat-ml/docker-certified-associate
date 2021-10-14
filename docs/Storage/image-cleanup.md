
1.Display the storage space being used by Docker.
```
docker system df
```
2.Display disk usage by individual objects.
```
docker system df -v
```
3.Delete dangling images (images with no tags or containers).
```
docker image prune
```
4.Pull an image not being used by any containers, and use docker image prune -a to clean up all images with no
containers.
```
docker image pull nginx:1.14.0
docker image prune -a
```