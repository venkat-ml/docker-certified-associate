

######Limit Root Access to Node

Docker Enterprise uses a completely separate authentication backend from the host, providing a clear separation of duties. Docker Enterprise can leverage an existing LDAP/AD infrastructure for authentication. It even utilizes RBAC Labels to control access to objects like images and running containers, meaning teams of users can be given full access to running containers. With this access, users can watch the logs and execute a shell inside the running container without needing to ever log into the host. Limiting the number of users that have access to the host reduces the attack surface.


###### --cap-drop, --cap-add
Linux capabilities are an even more granular way of reducing surface area. Docker Engine has a default list of capabilities that are kept for newly-created containers, and by using the --cap-drop option for docker run, users can exclude additional capabilities from being used by processes inside the container on a capability-by-capability basis. All privileges can be dropped with the --user option.

Likewise, capabilities that are, by default, not granted to new containers can be added with the --cap-add option. This is discouraged unless absolutely necessary, and using --cap-add=ALL is highly discouraged.
Limiting the capabilities of a container reduces the attack surface.

######Installation of docker engine

You can install Docker Engine in different ways, depending on your needs:

- Most users set up Docker’s repositories and install from them, for ease of installation and upgrade tasks. This is the recommended approach.

- Some users download the RPM package and install it manually and manage upgrades completely manually. This is useful in situations such as installing Docker on air-gapped systems with no access to the internet.

- In testing and development environments, some users choose to use automated convenience scripts to install Docker.

######Docker Enterprise components

Docker Enterprise has three major components, which together enable a full software supply chain, from image creation, to secure image storage, to secure image deployment.

**Docker Engine - Enterprise:** The commercially supported Docker engine for creating images and running them in Docker containers.

**Docker Trusted Registry (DTR):** The production-grade image storage solution from Docker.

DTR is designed to scale horizontally as your usage increases. You can add more replicas to make DTR scale to your demand and for high availability.

All DTR replicas run the same set of services, and changes to their configuration are propagated automatically to other replicas.

**Universal Control Plane (UCP):** Deploys applications from images, by managing orchestrators, like Kubernetes and Swarm.

UCP is designed for high availability (HA). You can join multiple UCP manager nodes to the cluster, and if one manager node fails, another takes its place automatically without impact to the cluster.

Changes to the configuration of one UCP manager node are propagated automatically to other nodes.

######Container format

Docker Engine combines the namespaces, control groups, and UnionFS into a wrapper called a container format. The default container format is libcontainer.

######Backup

To back up Docker Enterprise, you must create individual backups for each of the following components:

  1. Back up Docker Swarm. Back up Swarm resources like service and network definitions.

  2. Back up Universal Control Plane (UCP). Back up UCP configurations such as Access control, Certificates and keys, volumes = All UCP named volumes, which include all UCP component certs and data, Monitoring data gathered by UCP,

  3. Back up Docker Trusted Registry (DTR). Back up DTR configurations, images, and metadata, Repository metadata, Access control to repos and images, Notary data, Scan results.

If you do not create backups for all components, you cannot restore your deployment to its previous state.

Role-Based Access Control (RBAC) is part of UCP.

######Bundle
A user's Docker bundle includes all the environment files and certificates required for using the CaaS platform.

######Team’s permissions
Permissions are cumulative. For example, if you have Write permissions, you automatically have Read permissions:

- Read access allows users to view, search, and pull a private repository in the same way as they can a public repository.

- Write access allows users to push to repositories on Docker Hub.

- Admin access allows users to modify the repositories “Description”, “Collaborators” rights, “Public/Private” visibility, and “Delete”.

######webhooks
You can use webhooks to cause an action in another service in response to a push event in the repository. Webhooks are POST requests sent to a URL you define in Docker Hub.

orchestrator type of a newly promoted worker node - Mixed

