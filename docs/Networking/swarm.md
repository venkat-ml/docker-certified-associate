


######Docker Swarm

By default, Docker Swarm uses a default address pool 10.0.0.0/8 for global scope (overlay) networks.

Every network that does not have a subnet specified will have a subnet sequentially allocated from this pool. In some circumstances it may be desirable to use a different default IP address pool for networks.

For example, if the default 10.0.0.0/8 range conflicts with already allocated address space in your network, then it is desirable to ensure that networks use a different range without requiring Swarm users to specify each subnet with the --subnet command.

https://docs.docker.com/engine/swarm/swarm-mode/#configuring-default-address-pools

- **Docker Swarm Task states**

    - Create a service by using docker service create.
    - The request goes to a Docker manager node.
    - The Docker manager node schedules the service to run on particular nodes.
    - Each service can start multiple tasks.
    - Each task has a life cycle, with states like NEW, PENDING, and COMPLETE.

- There is no limit on the number of manager nodes.

  The decision about how many manager nodes to implement is a trade-off between performance and fault-tolerance.
  
  Adding manager nodes to a swarm makes the swarm more fault-tolerant. 
  
  However, additional manager nodes reduce write performance because more nodes must acknowledge proposals to update the swarm state. This means more network round-trip traffic.
  
  
- **Raft consensus in swarm mode**

    The reason why Docker swarm mode is using a consensus algorithm is to make sure that all the manager nodes that are in charge of managing and scheduling tasks in the cluster, are storing the same consistent state.

    Raft tolerates up to (N-1)/2 failures and requires a majority or quorum of (N/2)+1 members to agree on values proposed to the cluster.

######Docker Service Discovery

Docker uses embedded DNS to provide service discovery for containers running on a single Docker engine and tasks running in a Docker swarm.

The Docker engine checks if the DNS query belongs to a container or service on each network that the requesting container belongs to. If it does, then the Docker engine looks up the IP address that matches the name of a container, task, or service in a key-value store and returns that IP or service Virtual IP (VIP) back to the requester. Here, all nodes participate in a network called routing mesh.

https://success.docker.com/article/ucp-service-discovery-swarm

DNS round robin (DNS RR) load balancing is another load balancing option for services (configured with --endpoint-mode dnsrr). In DNS RR mode a VIP is not created for each service. The Docker DNS server resolves a service name to individual container IPs in round robin fashion.

https://success.docker.com/article/networking

To use an external load balancer without the routing mesh, set --endpoint-mode to dnsrr instead of the default value of vip.

In this case, there is not a single virtual IP. Instead, Docker sets up DNS entries for the service such that a DNS query for the service name returns a list of IP addresses, and the client connects directly to one of these. You are responsible for providing the list of IP addresses and ports to your load balancer. See Configure service discovery.

https://docs.docker.com/engine/swarm/ingress/#without-the-routing-mesh

Services using the routing mesh are running in virtual IP (VIP) mode. Even a service running on each node (by means of the --mode global flag) uses the routing mesh. When using the routing mesh, there is no guarantee about which Docker node services client requests.

To bypass the routing mesh, you can start a service using DNS Round Robin (DNSRR) mode, by setting the --endpoint-mode flag to dnsrr.

https://docs.docker.com/network/overlay/#bypass-the-routing-mesh-for-a-swarm-service



#####Routing Mesh vs Host Mode

When you create a swarm service, you can publish that service’s ports to hosts outside the swarm in two ways:
 
 - You can rely on the routing mesh. When you publish a service port, the swarm makes the service accessible at the target port on every node, regardless of whether there is a task for the service running on that node or not. This is less complex and is the right choice for many types of services.

 - You can publish a service task’s port directly on the swarm node where that service is running. This feature is available in Docker 1.13 and higher. This bypasses the routing mesh and provides the maximum flexibility, including the ability for you to develop your own routing framework. However, you are responsible for keeping track of where each task is running and routing requests to the tasks, and load-balancing across the nodes.

By default, services are using the routing mesh which makes the service accessible at the published port on every swarm node. When a user or process connects to a service, any worker node running a service task may respond.

To publish a service’s port directly on the node where it is running, use the mode=host option to the --publish flag.

For example:
```
$ docker service create \
  --mode global \
  --publish mode=host,target=80,published=8080 \
  --name=nginx \
  nginx:latest
```
  
https://docs.docker.com/engine/swarm/services/#publish-ports

    
**Concepts**

- By default Docker Swarm uses a default address pool 10.0.0.0/8 for global scope (overlay) networks.

- If --default-addr-pool-mask-len were unspecified or set explicitly to 24, this would result in 256 /24 networks of the form 10.10.X.0/24.

- Demote manager node to worker node and docker swarm leave

- Lock your swam to protect its encryption key

- You don’t need to unlock the swarm when a new node joins the swarm, because the key is propagated to it over mutual TLS.

- When Docker restarts, you must unlock the swarm first, using a key encryption key generated by Docker when the swarm was locked. You can rotate this key encryption key at any time.

- When you rotate the unlock key, keep a record of the old key around for a few minutes, so that if a manager goes down before it gets the new key, it may still be unlocked with the old one.

- Worker node can join the cluster without the need of autolock key

- A node is an instance of the Docker engine participating in the swarm. 

- Setting a node to DRAIN does not remove standalone containers from that node, such as those created with docker run, docker-compose up, or the Docker Engine API.

- A node’s status, including DRAIN, only affects the node’s ability to schedule swarm service workloads.
    https://docs.docker.com/engine/swarm/swarm-tutorial/drain-node/

- DRAIN availability prevents a node from receiving new tasks from the swarm manager. It also means the manager stops tasks running on the node and launches replica tasks on a node with ACTIVE availability.
  https://docs.docker.com/engine/swarm/swarm-tutorial/drain-node/

- Sometimes, such as planned maintenance times, you need to set a node to DRAIN availability.

- DRAIN availability prevents a node from receiving new tasks from the swarm manager.

- It also means the manager stops tasks running on the node and launches replica tasks on a node with ACTIVE availability.


Docker manager nodes store the swarm state and manager logs in the /var/lib/docker/swarm/ directory.

Usually, on Linux distributions:

"/etc" is used for configurations (.conf files etc). here you find all the configs and settings for your system.

"/var" is usually used for log files, 'temporary' files (like mail spool, printer spool, etc), databases, and all other data not tied to a specific user. Logs are usually in "/var/log", databases in "/var/lib" (mysql - "/var/lib/mysql"), etc.

**Ingress**

When you create a swarm service and do not connect it to a user-defined overlay network, it connects to the ingress network by default.

The ingress network is created without the --attachable flag, which means that only swarm services can use it, and not standalone containers.
You can connect standalone containers to user-defined overlay networks which are created with the --attachable flag.

https://docs.docker.com/network/overlay/#attach-a-standalone-container-to-an-overlay-network

The routing mesh allows all the swarm nodes to accept connections on the services published ports. When any swarm node receives traffic destined to the published TCP/UDP port of a running service, it forwards the traffic to the service's VIP using a pre-defined overlay network called ingress. The ingress network behaves similarly to other overlay networks, but its sole purpose is to transport mesh routing traffic from external clients to cluster services. It uses the same VIP-based internal load balancing as described in the previous section.

https://success.docker.com/article/ucp-service-discovery-swarm#interlockproxyusageexamples

Docker Engine swarm mode makes it easy to publish ports for services to make them available to resources outside the swarm.

All nodes participate in an ingress routing mesh.

The routing mesh enables each node in the swarm to accept connections on published ports for any service running in the swarm, even if there’s no task running on the node.

The routing mesh routes all incoming requests to published ports on available nodes to an active container.

https://docs.docker.com/engine/swarm/ingress/

######Initialise a swarm

When you initialize a swarm or join a Docker host to an existing swarm, two new networks are created on that Docker host:
- an overlay network called ingress, which handles control and data traffic related to swarm services. When you create a swarm service and do not connect it to a user-defined overlay network, it connects to the ingress network by default.
- a bridge network called docker_gwbridge, which connects the individual Docker daemon to the other daemons participating in the swarm.

######autolock

Docker 1.13 introduces the ability to protect the mutual TLS encryption key and the key used to encrypt and decrypt secrets, by allowing you to take ownership of these keys and to require manual unlocking of your managers. This feature is called autolock.

When Docker restarts, you must unlock the swarm first, using a key encryption key generated by Docker when the swarm was locked. You can rotate this key encryption key at any time.

To enable autolock on an existing swarm, set the autolock flag to true.
docker swarm update --autolock=true
https://docs.docker.com/engine/swarm/swarm_manager_locking/#enable-or-disable-autolock-on-an-existing-swarm

######Secrets 

Sensitive information to containers running on a Swarm are normally stored in a secret.

A secret (usually containing credentials, certificates, and other private information) is provided to service at runtime. The secret is saved in the Raft logs.

The Raft logs used by swarm managers are encrypted on disk by default.

When Docker restarts, both the TLS key used to encrypt communication among swarm nodes, and the key used to encrypt and decrypt Raft logs on disk, are loaded into each manager node’s memory.

Docker 1.13 introduces the ability to protect the mutual TLS encryption key and the key used to encrypt and decrypt Raft logs at rest, by allowing you to take ownership of these keys and to require manual unlocking of your managers. This feature is called autolock.

When Docker restarts, you must unlock the swarm first, using a key encryption key generated by Docker when the swarm was locked. You can rotate this key encryption key at any time.

When you initialize a new swarm, you can use the --autolock flag to enable autolocking of swarm manager nodes when Docker restarts.

```
docker swarm init --autolock
```

To enable autolock on an existing swarm, set the autolock flag to true.

```
docker swarm update --autolock=true
```

In terms of Docker Swarm services, a secret is a blob of data, such as a password, SSH private key, SSL certificate, or another piece of data that should not be transmitted over a network or stored unencrypted in a Dockerfile or in your application’s source code.
https://docs.docker.com/engine/swarm/secrets/#about-secrets

The command to create a secret from a file or standard input (STDIN) as content is:
```
docker secret create [OPTIONS] SECRET [file|-]
```
Since this is a cluster management command, it must be executed on a swarm manager node.

https://docs.docker.com/engine/reference/commandline/secret_create/

Note: After you create a secret, you cannot update it. You can only remove and re-create it, and you cannot remove a secret that a service is using.

https://docs.docker.com/engine/swarm/secrets/#advanced-example-use-secrets-with-a-wordpress-service

Secrets are encrypted during transit and at rest in a Docker swarm (The secret is stored in the Raft log, which is encrypted)

A Docker secret is a blob of sensitive data that should not be transmitted over a network, such as:
- Usernames and passwords
- TLS certificates and keys
- SSH keys
- Other important data such as the name of a database or internal server
- Generic strings or binary content (up to 500 kb in size)


- Secret files with custom targets are not directly bind-mounted into Windows containers, since Windows does not support non-directory file bind-mounts. 

- Instead, secrets for a container are all mounted in C:\ProgramData\Docker\internal\secrets (an implementation detail which should not be relied upon by applications) within the container. Symbolic links are used to point from there to the desired target of the secret within the container. The default target is C:\ProgramData\Docker\secrets.

- The location of the mount point within the container defaults to /run/secrets/<secret_name> in Linux containers, or C:\ProgramData\Docker\secrets in Windows containers. You can also specify a custom location.


**Add or remove secrets**

Use the --secret-add or --secret-rm options add or remove a service’s secrets.

For example:

```
docker service update \
 	--secret-rm mysql_password \
 	--secret-add source=mysql_password_v2,target=wp_db_password,mode=0400 \
 	wordpress
 	 	  
```
This triggers a rolling restart of the WordPress service and the new secret is used.

The following example adds a secret named ssh-2 and removes ssh-1:

```
$ docker service update \
	--secret-add source=ssh-2,target=ssh-2 \
	--secret-rm ssh-1 \
	Myservice
```

######docker swarm init

When you create a swarm by running docker swarm init, Docker designates itself as a manager node. By default, the manager node generates a new root Certificate Authority (CA) along with a key pair, which are used to secure communications with other nodes that join the swarm.

If you prefer, you can specify your own externally-generated root CA, using the --external-ca flag of the docker swarm init command.


######PKI

The swarm mode public key infrastructure (PKI) system built into Docker makes it simple to securely deploy a container orchestration system. The nodes in a swarm use mutual Transport Layer Security (TLS) to authenticate, authorize, and encrypt the communications with other nodes in the swarm.

######Rotating the CA certificate

In the event that a cluster CA key or a manager node is compromised, you can rotate the swarm root CA so that none of the nodes trust certificates signed by the old root CA anymore.

Run docker swarm ca --rotate to generate a new CA certificate and key. If you prefer, you can pass the --ca-cert and --external-ca flags to specify the root certificate and to use a root CA external to the swarm. Alternately, you can pass the --ca-cert and --ca-key flags to specify the exact certificate and key you would like the swarm to use.


######Control Plane Security

Docker Swarm comes with integrated PKI. All managers and nodes in the Swarm have a cryptographically signed identity in the form of a signed certificate. All manager-to-manager and manager-to-node control communication is secured out of the box with TLS. There is no need to generate certs externally or set up any CAs manually to get end-to-end control plane traffic secured in Docker Swarm mode. Certificates are periodically and automatically rotated.

######Backup swarm

To backup the Swarm on Linux using Docker Engine >= 17.03, you can use the following steps.

Since this will need you to stop the engine of a manager, your cluster need to be healthy with at least 3 managers.
1. Select a manager node to do the operation. Try not to choose the leader one in order to avoid a new election inside the cluster.

2. Optional: Store the Docker version to a variable for easy addition to your backup name.

3. Stop the Docker Engine on the manager before backing up the data, so that no data is being changed during the backup.

4. Backup the entire Swarm folder /var/lib/docker/swarm.

5. Restart the manager Docker Engine.

https://success.docker.com/article/backup-restore-swarm-manager

Docker manager nodes store the swarm state and manager logs in the /var/lib/docker/swarm/

https://docs.docker.com/engine/swarm/admin_guide/#back-up-the-swarm

"/etc" is used for configurations (.conf files etc). here you find all the configs and settings for your system.

"/var" is usually used for log files, 'temporary' files (like mail spool, printer spool, etc), databases, and all other data not tied to a specific user. Logs are usually in "/var/log", databases in "/var/lib" (mysql - "/var/lib/mysql"), etc.

https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard


######join worker node

The Docker Engine joins the swarm depending on the join-token you provide to the docker swarm join command.

To retrieve the join command including the join token for worker nodes, run the following command on a manager node:
```
$ docker swarm join-token worker
```
To add a worker to this swarm, run the following command with the output from the previous command:
	
	
	docker swarm join \
	--token SWMTKN-1-49nj1cmql0jkz5s954yi3oex3nedyz0fb0xx14ie39trti4wxv-8vxv8rssmk743ojnwacrr2e7c \
	192.168.99.100:2377
	

This node joined a swarm as a worker.

###### --force-new-cluster

This flag forces an existing node that was part of a quorum that was lost to restart as a single node Manager without losing its data.

######Routing Mesh

By default, ports are published using ingress mode.

This means that the swarm routing mesh makes the service accessible at the published port on every node regardless if there is a task for the service running on the node.

However, if you set host mode explicitly, the port is only bound on nodes where the service is running, and a given port on a node can only be bound once. You can only set the publication mode using the long syntax.

You can publish service ports to make them available externally to the swarm using the --publish flag.

The --publish flag can take two different styles of arguments.

The short version is positional and allows you to specify the published port and target port separated by a colon (:).
```
$ docker service create --name my_web --replicas 3 --publish 8080:80 nginx
```

Here, the published port is 8080 and the target port is of the container is 80.

There is also a long format, which is easier to read and allows you to specify more options.

```
$ docker service create --name my_web --replicas 3 --publish published=8080,target=80 nginx
```

######Basics

- Global services provide high availability
- Draining a node will remove global service
- Replicated services can be stopped by keeping --replicas to 0
- Default swarm mode is replicated
- Swarm Backup 
   
   a) Stop Docker Engine on any manager to ensure files are static.
   
   b) Copy the /var/lib/docker/swarm directory content for backing up Swarm.
- Replicated services
    
    c) They can be stopped using the Docker service update: --replicas 0 <SERVICENAME>.
    
    d) We will use Go templates to be able to provide unique resources, such as volumes or hostnames, inside containers to ensure all replicas use their own resources.

- Methods used to publish applications on Docker EE
    
    a) We can use Interlock.
    
    b) We will publish each application container.
    
    c) We can use the host mode to publish applications as if they were running directly at the host level.

######Which concept is responsible for managing external to internal load balancing for Docker Swarm services?
    a) Router Mesh
    
######Which of the following is true about secrets?
	a) They are ephemeral and deployed on on-memory filesystems.
	b) They are encrypted even for administrators, so they cannot be recovered from the control plane.
	c) If we need to change a secret, we need to create a new secret and update the service with this new one.


######Encrypt traffic on an overlay network

All swarm service management traffic is encrypted by default, using the **AES algorithm in GCM mode**. Manager nodes in the swarm rotate the key used to encrypt gossip data every 12 hours.

To encrypt application data as well, add --opt encrypted when creating the overlay network. This enables IPSEC encryption at the level of the vxlan. This encryption imposes a non-negligible performance penalty, so you should test this option before using it in production.

When you enable overlay encryption, Docker creates IPSEC tunnels between all the nodes where tasks are scheduled for services attached to the overlay network. These tunnels also use the AES algorithm in GCM mode and manager nodes automatically rotate the keys every 12 hours.

Do not attach Windows nodes to encrypted overlay networks.

Overlay network encryption is not supported on Windows. If a Windows node attempts to connect to an encrypted overlay network, no error is detected but the node cannot communicate.


######generate new CA certificate
commands is used to generate a new root CA certificate and root CA key for the swarm cluster?
```
docker swarm ca --rotate
```    
