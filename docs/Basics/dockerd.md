
#####dockerd

By using dockerd you can start the Docker daemon manually. 

On a typical installation the Docker daemon is started by a system utility, not manually by a user. 

However, this command can be useful to test things out and for troubleshooting problems.

To configure the DNS servers for all Docker containers you have to set the configuration at Docker daemon level.

By using dockerd command you can also configure it using flags.

For example, to set the DNS server for all Docker containers, use:
```
dockerd --dns IP_ADDRESS
```

**To specify multiple DNS servers, use multiple --dns flags.**

If the container cannot reach any of the IP addresses you specify, Google’s public DNS server 8.8.8.8 is added, so that your container can resolve internet domains.

http://dockerlabs.collabnix.com/beginners/components/daemon/
https://docs.docker.com/config/containers/container-networking/

######SIGUSR1

If docker daemon is unresponsive, user can send SIGUSR1 signal to docker daemon to force stack trace on daemon to list out all the logs of docker daemon till the point when stack trace was logged in. Stack trace will list out all threads running in docker daemon and helps to find the location of the potential error without terminating daemon process.

######Concurrent uploads

By default the Docker daemon will push five layers of an image at a time. 

If you are on a low bandwidth connection this may cause timeout issues and you may want to lower this via the --max-concurrent-uploads daemon option.

Container inherits default DNS settings from docker daemon

######Concurrent downloads

By default the Docker daemon will pull three layers of an image at a time. If you are on a low bandwidth connection this may cause timeout issues and you may want to lower this via the --max-concurrent-downloads daemon option. See the daemon documentation for more details.

######windowsfilter
On Windows, the Docker daemon supports a single image layer storage driver depending on the image platform: windowsfilter for Windows images, and lcow for Linux containers on Windows.

######Listen on multiple ports

You can configure the Docker daemon to listen to multiple sockets at the same time using multiple -H options:

The example below runs the daemon listenin on the default unix socket, and on 2 specific IP addresses on this host:
```
$ sudo dockerd -H unix:///var/run/docker.sock -H tcp://192.168.59.106 -H tcp://10.10.10.2
```
