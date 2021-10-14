
```bash
docker service ps       # to list the tasks

docker service inspect  # to inspect the service for configuration details

docker service update --publish-rm 80 my_web   # There is nothing like port in command line argument flags

docker run -d --restart unless-stopped redis   # There is nothing like policy in command line argument flags

docker node update --label-add

docker node update --label-rm
```

There are no volumes for services.. We can specify mount using --mount flag.

The type of mount can be either volume, bind, tmpfs, or npipe. Defaults to volume if no type is specified.


```bash
$ docker service create \
--name my-service \
--replicas 3 \
--mount type=volume,source=my-volume,destination=/path/in/container \
nginx:alpine
```

Examples of services might include an HTTP server, a database, or any other type of executable program that you wish to run in a **distributed** environment.

```bash
docker service create --constraint-add

docker service create --constraint-rm

docker service create --placement-pref-add

docker service create --placement-pref-rm
```

Sometimes, such as planned maintenance times, you need to set a node to DRAIN availability.

DRAIN availability prevents a node from receiving new tasks from the swarm manager.

It also means the manager stops tasks running on the node and launches replica tasks on a node with ACTIVE availability.

```bash
docker service create \
    --name nginx-workers-only \
    --constraint node.role==worker \
    nginx
```

**docker service scale** 
- can scale up or down one or multiple services
- can only work on replicated services

```bash
docker service scale backend=3 frontend=5

docker service ls
```

Node can only be drained for maintanence

```bash
docker node update --availability drain node-1
```

Placement prefs allow you to "spread" your replicas across nodes with certain tags, choosing to put the replicas as much "diverse" as possible.

Constraints will constrain (limit) the replicas to those nodes that match the constraint.


```bash
docker service create \
  --replicas 2 \
  --name webserver \
  --placement-pref 'spread=node.labels.datacenter' \
  mywebservice:production
```

```bash
docker service create \
  --constraint 'node.labels.disk == ssd' \
  myreporter:latest
```

**Tip:**
There is nothing like --set in docker commandline arguments

**Tip:**
There is nothing like --filter in docker inspect, it is always --format

**Tip:**
There is nothing like docker xxx info.. There is only _**docker (system) info**_

**Tip:**
There is nothing like _docker list_

**Tip:**
There is nothing like --add, --set, --custom

**Tip:**
There is nothing like --networks or --list

**Tip:**
There is nothing like --ignore-tls

**DTR image deletion**

DTR only allows deleting images if the image has not been signed. You first need to delete all the trust data associated with the image before you are able to delete the image.

**HEALTHCHECK**

The HEALTHCHECK instruction has two forms:

HEALTHCHECK [OPTIONS] CMD command (check container health by running a command inside the container)
HEALTHCHECK NONE (disable any healthcheck inherited from the base image)
The HEALTHCHECK instruction tells Docker how to test a container to check that it is still working. This can detect cases such as a web server that is stuck in an infinite loop and unable to handle new connections, even though the server process is still running.

When a container has a healthcheck specified, it has a health status in addition to its normal status. This status is initially starting. Whenever a health check passes, it becomes healthy (whatever state it was previously in). After a certain number of consecutive failures, it becomes unhealthy.

**Best Practice**

The FROM instruction initializes a new build stage and sets the Base Image for subsequent instructions.

As such, a valid Dockerfile must start with a FROM instruction.

ARG is the only instruction that may precede FROM in the Dockerfile.

For example:

ARG CODE_VERSION=latest
FROM base:${CODE_VERSION}

**Best Practice**

There can only be one CMD instruction in a Dockerfile. If you list more than one CMD then only the last CMD will take effect.

The main purpose of a CMD is to provide defaults for an executing container.

**Build context is different from Dockerfile**

The docker build command builds an image from a Dockerfile and a context.

The build’s context is the set of files at a specified location PATH or URL. The PATH is a directory on your local filesystem. The URL is a Git repository location.

A context is processed recursively. So, a PATH includes any subdirectories and the URL includes the repository and its submodules.

This example shows a build command that uses the current directory as context:

```bash
$ docker build .
Sending build context to Docker daemon  6.51 MB
...
```
You use the -f flag with docker build to point to a Dockerfile anywhere in your file system.

```bash
$ docker build -f /path/to/a/Dockerfile .
```
The build is run by the Docker daemon. The first thing a build process does is send the entire context (recursively) to the daemon.

In most cases, it’s best to start with an empty directory as context and keep your Dockerfile in that directory. Add only the files needed for building the Dockerfile.

https://docs.docker.com/engine/reference/builder/

**To show untagged images, or dangling, use:**

```bash
docker images --filter "dangling=true"
```

**DTR Backup Process**

_DTR content backed-up_

· Configurations - DTR settings and cluster configurations

· Repository metadata - Metadata such as image architecture, repositories, images deployed, and size

· Access control to repos and images - Data about who has access to which images and repositories

· Notary data - Signatures and digests for images that are signed

· Scan results - Information about vulnerabilities in your images

· Certificates and keys - Certificates, public keys, and private keys that are used for mutual TLS communication

_DTR content NOT backed-up_

· Image content - The images you push to DTR. This can be stored on the file system of the node running DTR, or other storage system, depending on the configuration. Needs to be backed up separately, depends on DTR configuration

· Users, orgs, teams - Create a UCP backup to back up this data

· Vulnerability database - Can be redownloaded after a restore


**Logging Mechanism**

Docker provides two modes for delivering messages from the container to the log driver:

· (default) direct, blocking delivery from container to driver

· non-blocking delivery that stores log messages in an intermediate per-container ring buffer for consumption by driver

https://docs.docker.com/config/containers/logging/configure/#configure-the-delivery-mode-of-log-messages-from-container-to-log-driver


**Start the daemon manually**

If you don’t want to use a system utility to manage the Docker daemon, or just want to test things out, you can manually run it using the dockerd command. You may need to use sudo, depending on your operating system configuration.

When you start Docker this way, it runs in the foreground and sends its logs directly to your terminal.

```bash
$ dockerd
INFO[0000] +job init_networkdriver()
INFO[0000] +job serveapi(unix:///var/run/docker.sock)
INFO[0000] Listening for HTTP on unix (/var/run/docker.sock)
To stop Docker when you have started it manually, issue a Ctrl+C in your terminal
```

In Docker swarm, traffic can be routed to services based on hostname - Swarm Layer7 routing


A stack is a collection of services that run on Swarm.

Backing up data from one manager node is enough in a UCP.

.dockerignore available both in community and enterprise edition.

Any 2 processes running on the same host cannot bind to the same port

**Configuring default address pools🔗**

By default Docker Swarm uses a default address pool 10.0.0.0/8 for global scope (overlay) networks. Every network that does not have a subnet specified will have a subnet sequentially allocated from this pool. In some circumstances it may be desirable to use a different default IP address pool for networks.

Docker allocates subnet addresses from the address ranges specified by the --default-addr-pool option. For example, a command line option --default-addr-pool 10.10.0.0/16 indicates that Docker will allocate subnets from that /16 address range. If --default-addr-pool-mask-len were unspecified or set explicitly to 24, this would result in 256 /24 networks of the form 10.10.X.0/24


- Image signing and Security Scanning are part of DTR

- If key is compromised, then rotate the key in 