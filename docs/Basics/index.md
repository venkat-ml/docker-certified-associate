
- Default logging driver : json-file

- The following ports must be available. On some systems, these ports are open by default.

    - TCP port 2377 for cluster management communications
    - TCP and UDP port 7946 for communication among nodes
    - UDP port 4789 for overlay network traffic
    
- Secrets can be used by Swarm services, not standalone containers.

- **Enable live restore**

    There are two ways to enable the live restore setting to keep containers alive when the daemon becomes unavailable. Only do one of the following.

    Add the configuration to the daemon configuration file. On Linux, this defaults to /etc/docker/daemon.json. On Docker Desktop for Mac or Docker Desktop for Windows, select the Docker icon from the task bar, then click Preferences -> Daemon -> Advanced.
        
             Use the following JSON to enable live-restore.
          
            {
              "live-restore": true
            }
          
          
    Restart the Docker daemon. On Linux, you can avoid a restart (and avoid any downtime for your containers) by reloading the Docker daemon. If you use systemd, then use the command systemctl reload docker. Otherwise, send a SIGHUP signal to the dockerd process.
    
        sudo kill -SIGHUP $(pid_of_dockerd)


   If you prefer, you can start the dockerd process manually with the --live-restore flag. This approach is not recommended because it does not set up the environment that systemd or another process manager would use when starting the Docker process. This can cause unexpected behavior.

- A grant is made up of subject, role and resource set.

- Grants define which users can access what resources in what way. 
Grants are effectively ACLs, and when grouped together, they provide comprehensive access policies for an entire organisation.

- Only admin can manage grants, subjects, roles and access to resources.

- Secrets are stored at /run/secrets

- Default location of config file is /

- docker.sock owner is root and default permissions are 660 (rw-rw-----)

- docker run -m -> to limit the memory

- --privileged flag is used to enable all kernel capabilities, which enables a process running within a Docker container to bypass most of the controls such as kernel namespaces and cgroups limitations.

- Blocklevel storage drivers - devicemapper, btrfs, zfs

- Process spawned by Docker are run using root user.

- Secrets are encrypted at rest

- Config is transmitted encrypted to the manager.

- Tmpfs mount are available for linux.

- loop-lvm - testing, direct-lvm - production

- Swarm, UCP, DTR - order of backup and recovery

- Can’t back metrics and monitoring data


- A task is a one-directional mechanism.

    This means that it progresses monotonically through a series of states: assigned, prepared, running, etc. If the task fails the orchestrator removes the task and its container and then creates a new task to replace it according to the desired state specified by the service.

- There are different kinds of resources for creating Pods:
   
   - Use a Deployment, ReplicaSet or StatefulSet for Pods that are not expected to terminate, for example, web servers.
   - Use a Job for Pods that are expected to terminate once their work is complete; for example, batch computations. 
   - Jobs are appropriate only for Pods with restartPolicy equal to OnFailure or Never.
   - Use a DaemonSet for Pods that need to run one per eligible node.

- Like a Deployment, a StatefulSet manages Pods that are based on an identical container spec. Unlike a Deployment, a StatefulSet maintains a sticky identity for each of their Pods. These pods are created from the same spec, but are not interchangeable: each has a persistent identifier that it maintains across any rescheduling.

- To configure the restart policy for a container, use the --restart flag when using the docker run command. The value of the --restart flag can be any of the following:
    - no - Do not automatically restart the container. (the default)
    - on-failure - Restart the container if it exits due to an error, which manifests as a non-zero exit code.
    - always - Always restart the container if it stops. If it is manually stopped, it is restarted only when Docker daemon restarts or the container itself is manually restarted. (See the second bullet listed in restart policy details)
    - unless-stopped - Similar to always, except that when the container is stopped (manually or otherwise), it is not restarted even after Docker daemon restarts.

    https://docs.docker.com/config/containers/start-containers-automatically/
    
 - A Secret is an object that contains a small amount of sensitive data such as a password, a token, or a key. Such information might otherwise be put in a Pod specification or in an image. Users can create secrets and the system also creates some secrets.

    To use a secret, a Pod needs to reference the secret. A secret can be used with a Pod in three ways:
    
        - As files in a volume mounted on one or more of its containers.
        - As container environment variable.
        - By the kubelet when pulling images for the Pod.
    
- By default, a container has no resource constraints and can use as much of a given resource as the host’s kernel scheduler allows. Docker provides ways to control how much memory, or CPU a container can use, setting runtime configuration flags of the docker run command.
    
- Best practices for writing Dockerfiles:
    - Create ephemeral containers
    - Understand build context
    - Pipe Dockerfile through stdin
    - Exclude with .dockerignore
    - Use multi-stage builds
    - Don’t install unnecessary packages
    - Decouple applications
    - Minimize the number of layers
    - Sort multi-line arguments
    - Leverage build cache
    
- The default filename is Dockerfile (without an extension), and using the default can make various tasks easier while working with containers.

- CMD instruction allows you to set a default command, which will be executed only when you run container without specifying a command. If Docker container runs with a command, the default command will be ignored. If Dockerfile has more than one CMD instruction, all but last CMD instructions are ignored. The main purpose of a CMD is to provide defaults for an executing container.

- ENTRYPOINT instruction allows you to configure a container that will run as an executable. It looks similar to CMD, because it also allows you to specify a command with parameters. The difference is ENTRYPOINT command and parameters are not ignored when Docker container runs with command line parameters. (There is a way to ignore ENTTRYPOINT, but it is unlikely that you will do it.)

- The ENV instruction sets the environment variable <key> to the value <value>. This value will be in the environment for all subsequent instructions in the build stage.

- Also, the environment variables set using ENV will persist when a container is run from the resulting image. You can view the values using docker inspect, and change them using docker run --env <key>=<value>.

- The ADD instruction operates similarly to the COPY instruction with two important differences.
- The ADD instruction will additionally support:
    - Fetch remote source files if a URL is specified
    - Auto-extraction of archive files. Extract the files of any source that are determined to be of archive file type.

- The EXPOSE instruction informs Docker that the container listens on the specified network ports at runtime. You can specify whether the port listens on TCP or UDP, and the default is TCP if the protocol is not specified.

- The EXPOSE instruction does not actually publish the port. It functions as a type of documentation between the person who builds the image and the person who runs the container, about which ports are intended to be published. To actually publish the port when running the container, use the -p flag on docker run to publish and map one or more ports, or the -P flag to publish all exposed ports and map them to high-order ports.

- The RUN instruction will execute any commands in a new layer on top of the current image and commit the results. The resulting committed image will be used for the next step in the Dockerfile.

- CMD instruction allows you to set a default command, which will be executed only when you run container without specifying a command. If Docker container runs with a command, the default command will be ignored. If Dockerfile has more than one CMD instruction, all but last CMD instructions are ignored.

- Note: Don’t confuse RUN with CMD. RUN actually runs a command and commits the result; CMD does not execute anything at build time, but specifies the intended command for the image.

- When building an image, do not use your root directory, /, as the PATH as it causes the build to transfer the entire contents of your hard drive to the Docker daemon.
  
  The build is run by the Docker daemon, not by the CLI.
  
  The first thing a build process does is send the entire context (recursively) to the daemon.

  Build context : All the files found in the specific path or URL
  
######To create a tag TARGET_IMAGE that refers to SOURCE_IMAGE use:

```
docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
```
Tag an image referenced by ID

To tag a local image with ID “0e5574283393” into the “fedora” repository with “version1.0”:
```
$ docker tag 0e5574283393 fedora/httpd:version1.0
```

Tag an image referenced by Name

To tag a local image with name “httpd” into the “fedora” repository with “version1.0”:
```
$ docker tag httpd fedora/httpd:version1.0
```
Note that since the tag name is not specified, the alias is created for an existing local version httpd:latest.

Tag an image referenced by Name and Tag

To tag a local image with name “httpd” and tag “test” into the “fedora” repository with “version1.0.test”:
```
$ docker tag httpd:test fedora/httpd:version1.0.test
```

To show untagged images, or dangling, use:
```
docker images --filter "dangling=true"
```

This will display untagged images that are the leaves of the images tree (not intermediary layers). These images occur when a new build of an image takes the repo:tag away from the image ID, leaving it as <none>:<none> or untagged. A warning will be issued if trying to remove an image when a container is presently using it. By having this flag it allows for batch cleanup.

######DTR - Immutable tags

- By default, users with read and write access to a repository can push the same tag multiple times to that repository. For example, when user A pushes an image to library/wordpress:latest, there is no preventing user B from pushing an image with the same name but a completely different functionality. This can make it difficult to trace the image back to the build that generated it.

- To prevent tags from being overwritten, you can configure a repository to be immutable in the DTR web UI. Once configured, DTR will not allow anyone else to push another image tag with the same name.

######Healthcheck

The HEALTHCHECK instruction tells Docker how to test a container to check that it is still working. This can detect cases such as a web server that is stuck in an infinite loop and unable to handle new connections, even though the server process is still running.

The HEALTHCHECK instruction tells Docker how to test a container to check that it is still working.

The HEALTHCHECK instruction has two forms:
  ```
  HEALTHCHECK [OPTIONS] CMD command (check container health by running a command inside the container)
  HEALTHCHECK NONE (disable any healthcheck inherited from the base image)
  ```

When a container has a healthcheck specified, it has a health status in addition to its normal status.

The command after the CMD keyword can be either a shell command (e.g. HEALTHCHECK CMD /bin/check-running) or an exec array (as with other Dockerfile commands; see e.g. ENTRYPOINT for details)

######ARG vs ENV
The ARG instruction defines a variable that users can pass at build-time to the builder with the docker build command using the --build-arg <varname>=<value> flag.

https://docs.docker.com/engine/reference/builder/#arg

Unlike an ARG instruction, ENV values are always persisted in the built image.

The variable expansion technique in this example allows you to pass arguments from the command line and persist them in the final image by leveraging the ENV instruction.

You can use an ARG or an ENV instruction to specify variables that are available to the RUN instruction.

Environment variables defined using the ENV instruction always override an ARG instruction of the same name.

https://docs.docker.com/engine/reference/builder/#run#using-arg-variables


######LABEL
```
LABEL <key>=<value> <key>=<value> <key>=<value> ...
```

The LABEL instruction adds metadata to an image. A LABEL is a key-value pair.

Example:
LABEL version="1.0"

######docker search

To search the Docker Hub for images use:
```
docker search [OPTIONS] TERM
```

The filtering flag (-f or --filter) format is a key=value pair. If there is more than one filter, then pass multiple flags (e.g. --filter is-automated=true --filter stars=3)

The currently supported filters are:

```
stars (int - number of stars the image has)
is-automated (boolean - true or false) - is the image automated or not
is-official (boolean - true or false) - is the image official or not
```

https://docs.docker.com/engine/reference/commandline/search/#filtering

######RUN

The RUN instruction will execute any commands in a new layer on top of the current image and commit the results. The resulting committed image will be used for the next step in the Dockerfile.

Layering RUN instructions and generating commits conforms to the core concepts of Docker where commits are cheap and containers can be created from any point in an image’s history, much like source control.

Therefore, it’s ok to have more than one RUN instruction.

https://docs.docker.com/engine/reference/builder/#run

######docker save

To save one or more images to a tar archive (streamed to STDOUT by default) use:
```
docker save [OPTIONS] IMAGE [IMAGE...]
```
The achieve can be distributed through different channels such as: central file server, version-control system, sent it to you over email or shared it via flash drive.

https://docs.docker.com/engine/reference/commandline/save/

Docker provides a command to load images into Docker from a file. With this tool, you can load images that you acquired through other channels.

https://docs.docker.com/engine/reference/commandline/image_load/

######Multi stage builds

Multi-stage builds allow you to drastically reduce the size of your final image, without struggling to reduce the number of intermediate layers and files.

With multi-stage builds, you use multiple FROM statements in your Dockerfile. Each FROM instruction can use a different base, and each of them begins a new stage of the build. You can selectively copy artifacts from one stage to another, leaving behind everything you don’t want in the final image.

https://docs.docker.com/develop/develop-images/multistage-build/#use-multi-stage-builds


######Container vs Image

Fundamentally, a container is nothing but a running process, with some added encapsulation features applied to it in order to keep it isolated from the host and from other containers.

An image includes everything needed to run an application - the code or binary, runtimes, dependencies, and any other filesystem objects required.

######Info vs Inspect

```
docker info - system wide information

docker inspect <container>
```

######/etc/docker/daemon.json

- To make the modifications and send the HUP signal in one-line use:
```
echo '{"debug": true}' > /etc/docker/daemon.json ; sudo kill -HUP
```

######/var/run/docker.sock

- UNIX socket that Docker daemon is listening to. It's the main entry point for Docker API. It also can be TCP socket but by default for security reasons Docker defaults to use UNIX socket.

######privileged vs unprivileged containers

By default, Docker containers are “unprivileged” and cannot, for example, run a Docker daemon inside a Docker container. This is because by default a container is not allowed to access any devices, but a “privileged” container is given access to all devices (see the documentation on cgroups devices).

When the operator executes docker run --privileged, Docker will enable access to all devices on the host as well as set some configuration in AppArmor or SELinux to allow the container nearly all the same access to the host as processes running outside containers on the host. Additional information about running with --privileged is available on the Docker Blog.

If you want to limit access to a specific device or devices you can use the --device flag. It allows you to specify one or more devices that will be accessible within the container.
```
$ docker run --device=/dev/snd:/dev/snd ...
```
Following the least privileged principle, you should use --device instead of --privileged.

https://docs.docker.com/engine/reference/run/#/runtime-privilege-and-linux-capabilities

Docker Engine has a default list of capabilities for all newly created containers.


######Syslog

Having a central location for all Engine and container logs is recommended. This provides "off-node" access to all the logs, empowering developers without having to grant them SSH access.

To enable centralized logging, modify /etc/docker/daemon.json and add the following:

```
{
  "log-level": "syslog",
  "log-opts": {syslog-address=tcp://192.x.x.x}
}
```

Then restart the daemon:
```
sudo systemctl restart docker
```

######certificate authentication
You don’t need to run the docker client with sudo or the docker group when you use certificate authentication. That means anyone with the keys can give any instructions to your Docker daemon, giving them root access to the machine hosting the daemon. However, guard these keys as you would a root password!

######Copy-on-write strategy

When an existing file in a container is modified, the storage driver performs a copy-on-write operation. The specifics steps involved depend on the specific storage driver. For the aufs, overlay, and overlay2 drivers, the copy-on-write operation follows this rough sequence:

- Search through the image layers for the file to update. The process starts at the newest layer and works down to the base layer one layer at a time. When results are found, they are added to a cache to speed future operations.
- Perform a copy_up operation on the first copy of the file that is found, to copy the file to the container’s writable layer.
- Any modifications are made to this copy of the file, and the container cannot see the read-only copy of the file that exists in the lower layer.

######Image

A Docker image is built up from a series of layers. Each layer represents an instruction in the image’s Dockerfile.

Each layer except the very last one is read-only.

Each layer is only a set of differences from the layer before it.

######Docker namespaces

Docker namespaces provide a layer of isolation. Each aspect of a container runs in a separate namespace and its access is limited to that namespace.

Docker Engine uses namespaces such as the following on Linux:
 - The pid namespace: Process isolation (PID: Process ID).
 - The net namespace: Managing network interfaces (NET: Networking).
 - The ipc namespace: Managing access to IPC resources (IPC: InterProcess Communication).
 - The mnt namespace: Managing filesystem mount points (MNT: Mount).
 - The uts namespace: Isolating kernel and version identifiers. (UTS: Unix Timesharing System).
 
 
######logging mechanism

Docker includes multiple logging mechanisms to help you get information from running containers and services. These mechanisms are called logging drivers.

To find the current logging driver for a running container, run the docker inspect command, substituting the container name or ID for <CONTAINER>:
```
$ docker inspect <CONTAINER>
```

######cpuset-cpus

By default, each container’s access to the host machine’s CPU cycles is unlimited. You can set various constraints to limit a given container’s access to the host machine’s CPU cycles.

Several runtime flags allow you to configure the amount of access to CPU resources your container has. When you use these settings, Docker modifies the settings for the container’s cgroup on the host machine.

--cpuset-cpus

Limit the specific CPUs or cores a container can use. A comma-separated list or hyphen-separated range of CPUs a container can use, if you have more than one CPU. The first CPU is numbered 0. A valid value might be 0-3 (to use the first, second, third, and fourth CPU) or 1,3 (to use the second and fourth CPU).

https://docs.docker.com/config/containers/resource_constraints/#cpu

We can set cpus in which to allow execution for containers.

Examples:
```
$ docker run -it --cpuset-cpus="1,3" ubuntu:14.04 /bin/bash
```

This means processes in container can be executed on cpu 1 and cpu 3.
```
$ docker run -it --cpuset-cpus="0-2" ubuntu:14.04 /bin/bash
```

This means processes in container can be executed on cpu 0, cpu 1 and cpu 2.

https://docs.docker.com/engine/reference/run/#cpuset-constraint

######docker system events

Use docker system events to get real-time events from the server.

These events differ per Docker object type such as containers, images, plugins, volumes and daemons.


######enable/disable docker

Most current Linux distributions (RHEL, CentOS, Fedora, Ubuntu 16.04 and higher) use systemd to manage which services start when the system boots. Ubuntu 14.10 and below use upstart.
```
$ sudo systemctl enable docker
```
To disable this behavior, use disable instead.
```
$ sudo systemctl disable docker
```

######Control groups

Docker Engine on Linux also relies on another technology called control groups (cgroups).

A cgroup limits an application to a specific set of resources. Control groups allow Docker Engine to share available hardware resources to containers and optionally enforce limits and constraints.

For example, you can limit the memory or CPU available to a specific container.

######--cpus=<value>

Specify how much of the available CPU resources a container can use. For instance, if the host machine has two CPUs and you set --cpus="1.5", the container is guaranteed at most one and a half of the CPUs. This is the equivalent of setting --cpu-period="100000" and --cpu-quota="150000". Available in Docker 1.13 and higher.

######-m or --memory=
The maximum amount of memory the container can use. If you set this option, the minimum allowed value is 4m (4 megabyte).

Most of these options take a positive integer, followed by a suffix of b, k, m, g, to indicate bytes, kilobytes, megabytes, or gigabytes.

######log driver modes
Docker provides two modes for delivering messages from the container to the log driver:

- (default) direct, blocking delivery from container to driver
- non-blocking delivery that stores log messages in an intermediate per-container ring buffer for consumption by driver

######--default-ulimit

--default-ulimit allows you to set the default ulimit options to use for all containers. It takes the same options as --ulimit for docker run. If these defaults are not set, ulimit settings will be inherited, if not set on docker run, from the Docker daemon. Any --ulimit options passed to docker run will overwrite these defaults.

Be careful setting nproc with the ulimit flag as nproc is designed by Linux to set the maximum number of processes available to a user, not to a container. For details please check the run reference.

######Secure registry


- access images stored on a secure registry that is using a self-signed certificate
- configure our registry as "insecure" in Docker Engine's daemon.json file.
- to trust self-signed certificates. We will add DTR's created Certificate Authority (CA) into our system's trusted-CA list.

######docker image import

docker image import will only retrieve image layers containing binaries, libraries, and configurations for the process but without any meta-information about how to launch the process, what volumes to use, what ports should be used, and so on.

######How do we only list containers created from an alpine:3.10 image?
    a) docker ps --format ancestor=alpine:3.10
    b) docker container ls --filter ancestor=alpine:3.10

######Which of the following is true about privileged containers?
    a) Resource limits will be avoided (CPU, memory, and disk I/O).
    b) These containers run with all available capabilities.
    
######docker logs

The docker logs command batch-retrieves logs present at the time of execution.

Note This command is only functional for containers that are started with the json-file or journald logging driver.

######docker container logs vs docker logs

The docker logs command batch-retrieves logs present at the time of execution.

This command is only functional for containers that are started with the json-file or journald logging driver.

docker container logs - Fetch the logs of a container

######docker run
In the Dockerfile context, The RUN instruction will execute any commands in a new layer on top of the current image and commit the results.

Layering RUN instructions and generating commits conforms to the core concepts of Docker where commits are cheap and containers can be created from any point in an image’s history, much like source control.

https://docs.docker.com/engine/reference/builder/#run

Note: Don’t confuse RUN with CMD. RUN actually runs a command and commits the result; CMD does not execute anything at build time, but specifies the intended command for the image.


######How can we review ports published for a container named webserver?
       a) Using docker container ls --filter name=webserver.
       b) Using docker container port webserver.
       c) Using docker container inspect webserver --format="{{ .NetworkSettings.Ports }}".

######docker export
docker export command exports a container’s filesystem as a tar archive.


######docker rmi with multiple tags

If an image has one or more tags referencing it, you must remove all of them before the image is removed or you can use the -f flag and specify the image’s short or long ID, then this command untags and removes all images that match the specified ID.

```
docker rmi -f fd484f19954f
```

######docker update
The docker update command dynamically updates container configuration. You can use this command to prevent containers from consuming too many resources from their Docker host. With a single command, you can place limits on a single container or on many. To specify more than one container, provide space-separated list of container names or IDs.

######Find the cgroup for a given container

For each container, one cgroup is created in each hierarchy. On older systems with older versions of the LXC userland tools, the name of the cgroup is the name of the container. With more recent versions of the LXC tools, the cgroup is lxc/<container_name>.

Pause container - An empty container which holds cgroups, reservations, namespaces of a pod before its individual container is created.

Builder Pattern - It is a design pattern used to maintain 2 individual Dockerfiles for app development and production purposes.

######Configuration files🔗

By default, the Docker command line stores its configuration files in a directory called .docker within your $HOME directory.


######Antivirus software and Docker

When antivirus software scans files used by Docker, these files may be locked in a way that causes Docker commands to hang.

One way to reduce these problems is to add the Docker data directory (/var/lib/docker on Linux, %ProgramData%\docker on Windows Server, or $HOME/Library/Containers/com.docker.docker/ on Mac) to the antivirus’s exclusion list. However, this comes with the trade-off that viruses or malware in Docker images, writable layers of containers, or volumes are not detected. If you do choose to exclude Docker’s data directory from background virus scanning, you may want to schedule a recurring task that stops Docker, scans the data directory, and restarts Docker.

######Multistage Builds
```
$ docker build -t alexellis2/href-counter:latest .
$ docker build --target builder -t alexellis2/href-counter:latest .
```

- The container is assigned a separate IP address for every Docker network it connects to. The IP address is assigned from the pool assigned to the network, so the Docker daemon effectively acts as a DHCP server for each container.

- com.docker.network.bridge.host_binding_ipv4    --ip   Default IP when binding container ports

- The default value of the delay between two reset occurrences of a container - 100ms

- This command is only functional for containers that are started with the json-file or journald logging driver. (default logging drivers)

######docker service create
--update-parallelism Maximum number of tasks updated simultaneously (0 to update all at once)


######metrics
CPU metrics: cpuacct.stat

Memory metrics: memory.stat


######View task state

Run docker service ps <service-name> to get the state of a task. The CURRENT STATE field shows the task’s state and how long it’s been there.

```
$ docker service ps webserver
```
