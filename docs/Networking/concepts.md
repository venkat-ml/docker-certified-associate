
######Container Networking Model

There are several high-level constructs in the CNM. They are all OS and infrastructure agnostic so that applications can have a uniform experience no matter the infrastructure stack.

- **Sandbox** — A Sandbox contains the configuration of a container's network stack. This includes the management of the container's interfaces, routing table, and DNS settings. An implementation of a Sandbox could be a Windows HNS or Linux Network Namespace, a FreeBSD Jail, or other similar concept. A Sandbox may contain many endpoints from multiple networks.

- **Endpoint** — An Endpoint joins a Sandbox to a Network. The Endpoint construct exists so the actual connection to the network can be abstracted away from the application. This helps maintain portability so that a service can use different types of network drivers without being concerned with how it's connected to that network.

- **Network** — The CNM does not specify a Network in terms of the OSI model. An implementation of a Network could be a Linux bridge, a VLAN, etc. A Network is a collection of endpoints that have connectivity between them. Endpoints that are not connected to a network do not have connectivity on a network.

The sandbox perfectly isolates a container from the outside world. No inbound network connection is allowed into the sandboxed container. Yet, it is very unlikely that a container will be of any value in a system if absolutely no communication with it is possible. To work around this, we have element number two, which is the endpoint.

An endpoint is a controlled gateway from the outside world into the network's sandbox that shields the container. The endpoint connects the network sandbox, but not the container, to the third element of the model, which is the network. Also, the Endpoint construct exists so the actual connection to the network can be abstracted away from the application. This helps maintain portability so that a service can use different types of network drivers without being concerned with how it's connected to that network.


######User-defined bridges

- User-defined bridges provide automatic DNS resolution between containers.
- User-defined bridges provide better isolation.
- Containers can be attached and detached from user-defined networks on the fly.
- Each user-defined network creates a configurable bridge.
- Linked containers on the default bridge network share environment variables.

######Networking commandsd

**--publish-all or -P**

Publish all exposed ports to the host interfaces. Docker binds each exposed port to a random port on the host. 

Use the -p flag to explicitly map a single port or range of ports.

**-p or --publish**

It makes a port available to services outside of Docker, or to Docker containers which are not connected to the container’s network.

```
format: ip:hostPort:containerPort | ip::containerPort | hostPort:containerPort | containerPort
```


######IPAM Drivers
 - Docker has a native IP Address Management Driver that provides default subnets or IP addresses for the networks and endpoints if they are not specified. IP addressing can also be manually assigned through network, container, and service create commands. Remote IPAM drivers also exist and provide integration to existing IPAM tools.

######Encrypt traffic on an overlay network
All swarm service management traffic is encrypted by default, using the AES algorithm in GCM mode. Manager nodes in the swarm rotate the key used to encrypt gossip data every 12 hours.

To encrypt application data as well, add --opt encrypted when creating the overlay network. This enables IPSEC encryption at the level of the vxlan. This encryption imposes a non-negligible performance penalty, so you should test this option before using it in production.
https://docs.docker.com/network/overlay/

To set driver specific options use:
--opt , -o

For example:

```
$ docker network create -d overlay \
  --opt encrypted=true \
  my-network -network
```  

https://docs.docker.com/engine/reference/commandline/network_create/

######Network set to host

With the network set to host a container will share the host’s network stack and all interfaces from the host will be available to the container.

The container’s hostname will match the hostname on the host system.

Even in host network mode a container has its own UTS namespace by default.

As such --hostname and --domainname are allowed in host network mode and will only change the hostname and domain name inside the container.

https://docs.docker.com/engine/reference/run/#network-settings

######Macvlan Network

When you create a macvlan network, it can either be in bridge mode or 802.1q trunk bridge mode.
 
 - In bridge mode, macvlan traffic goes through a physical device on the host.
 - In 802.1q trunk bridge mode, traffic goes through an 802.1q sub-interface which Docker creates on the fly. This allows you to control routing and filtering at a more granular level.

https://docs.docker.com/network/macvlan/#create-a-macvlan-network

######Linux Bridges vs VXLAN

**Linux bridges** only operate locally and cannot span across nodes.

So, for multi-host networking we need another mechanism. Linux VXLAN comes to the rescue.

When a container sends a data packet, the bridge realizes that the target of the packet is not on this host.

Now, each node participating in an overlay network gets a so-called VXLAN Tunnel Endpoint (VTEP) object, which intercepts the packet (the packet at that moment is an OSI layer 2 data packet), wraps it with a header containing the target IP address of the host that runs the target container (this makes it now an OSI layer 3 data packet), and sends it over the VXLAN tunnel.

The VTEP on the other side of the tunnel unpacks the data packet and forwards it to the local bridge, which in turn forwards it to the target container.

######Create overlay network

To create an overlay network which can be used by swarm services or standalone containers to communicate with other standalone containers running on other Docker daemons, add the --attachable flag:
```
$ docker network create -d overlay --attachable my-attachable-overlay
```

######Nodeport vs Ingress

NodePort service type is well suited for application services that require direct TCP/UDP access, like RabbitMQ for example.

If your service can be accessed using HTTP/HTTPs then Ingress is recommended.

Ingress is a Kubernetes API object that manages external access to the services in a cluster, typically HTTP/HTTPS.


######Host network

If you use the host network mode for a container, that container’s network stack is not isolated from the Docker host (the container shares the host’s networking namespace), and the container does not get its own IP-address allocated.

For instance, if you run a container which binds to port 80 and you use host networking, the container’s application is available on port 80 on the host’s IP address.

The host networking driver only works on Linux hosts, and is not supported on Docker Desktop for Mac, Docker Desktop for Windows, or Docker EE for Windows Server.

Host mode networking can be useful to optimize performance, and in situations where a container needs to handle a large range of ports, as it does not require network address translation (NAT), and no “userland-proxy” is created for each port.

https://docs.docker.com/network/host/

######Which of the following sentences are true about overlay networks?
	   a) DTR deploys an overlay network, dtr-ol, to route a cluster's internal communications.
	   b) Overlay-defined networks are only present on manager nodes when there is not a task connected to them.
	   c) interlock-extension connects to services' defined networks to route requests to appropriate backends.
	   

The overlay2 driver natively supports up to 128 lower OverlayFS layers. This capability provides better performance for layer-related Docker commands such as docker build and docker commit, and consumes fewer inodes on the backing filesystem.	   
