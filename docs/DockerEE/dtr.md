
######Docker Trusted Registry (DTR)

Docker Trusted Registry can scan images in your repositories to verify that they are free from known security vulnerabilities or exposures, using Docker Security Scanning.

Docker Security Scanning is available as an add-on to Docker Trusted Registry, and an administrator configures it for your DTR instance.


######Image Immutability

As of DTR 2.3.0, there is an option to set a repository to Immutable. Setting a repository to Immutable means the tags can not be overwritten. This is a great feature for ensure the base images do not change over time. This next example is of the Alpine base image. Ideally CI would update the base image and push to DTR with a specific tag. Being Immutable simply guarantees that an authorized user can always go back to the specific tag and trust it has not changed. An Image Promotion Policy can extend on this.


######Insecure registries

While it’s highly recommended to secure your registry using a TLS certificate issued by a known CA, you can choose to use self-signed certificates, or use your registry over an unencrypted HTTP connection. Either of these choices involves security trade-offs and additional configuration steps.

This procedure configures Docker to entirely disregard security for your registry.

1. Edit the daemon.json file, whose default location is /etc/docker/daemon.json. Add the following content.
```
{
  "insecure-registries": ["myregistrydomain.com:5000"]
}
```
2. Restart Docker for the changes to take effect.

3. Repeat these steps on every Engine host that wants to access your registry.

Alternatively, you can pass the **--engine-insecure-registry** flag when the docker engine is started.


######Notary

As of Docker Engine 18.06 there is a docker trust command that will streamline the image signing process. 

The old is Notary. Notary is a tool for publishing and managing trusted collections of content. 

Publishers can digitally sign collections and consumers can verify integrity and origin of content. This ability is built on a straightforward key management and signing interface to create signed collections and configure trusted publishers.


######Vulnerabilities
From the Components view, the CVE number, a link to CVE database, file path, layers affected, severity, and description of severity are available.


######scan
Only users with write access to a repository can manually start a scan. Users with read-only access can view the scan results, but cannot start a new scan.

Image scan is a feature of Docker trusted Repository DTR, which is part of Docker Entreprise Edition.

To use the image scanning feature, make sure that you or your organization has purchased a DTR license that includes Docker Security Scanning, and that your Docker ID can access and download this license from the Docker Hub.

After you get the license, you’ll need to Enable DTR security scanning.

Docker Trusted Registry can scan images in your repositories to verify that they are free from known security vulnerabilities or exposures, using Docker Security Scanning.

Docker Security Scanning is available as an add-on to Docker Trusted Registry, and an administrator configures it for your DTR instance


######Garbage Collection

Garbage collection is an often-overlooked area from a security standpoint. Old, out-of-date images may contain security flaws or exploitable vulnerabilities; removing unnecessary images is important. Garbage collection is a feature that ensures that unreferenced images (and layers) are removed.

In the context of the Docker registry, garbage collection is the process of removing blobs from the filesystem when they are no longer referenced by a manifest. Blobs can include both layers and manifests.

Registry data can occupy considerable amounts of disk space. In addition, garbage collection can be a security consideration, when it is desirable to ensure that certain layers no longer exist on the filesystem.

######Install DTR

You can install DTR on-premises or on a cloud provider. To install DTR, all nodes must:

 - Be a worker node managed by UCP (Universal Control Plane). DTR replicas are installed one on each UCP worker node.

 - Have a fixed hostname.
 

######Networks used by DTR

To allow containers to communicate, when installing DTR the overlay networks should be created.

dtr-ol this allows DTR components running on different nodes to communicate, to replicate DTR data

######Health checks

DTR also exposes several endpoints you can use to assess if a DTR replica is healthy or not:

- **/health**: Checks if the several components of a DTR replica are healthy, and returns a simple json response. This is useful for load balancing or other automated health check tasks.

- **/load_balancer_status**: Checks if the several components of a DTR replica can be reached, and displays that information in a table. This is useful for an administrator to gauge the status of a DTR replica.

- **/nginx_status**: Returns the number of connections being handled by the NGINX front-end used by DTR.

- **/api/v0/meta/cluster_status**: Returns extensive information about all DTR replicas. 

######DTR content backed-up

- **Configurations** - DTR settings and cluster configurations
- **Repository metadata** - Metadata such as image architecture, repositories, images deployed, and size
- **Access control to repos and images** - Data about who has access to which images and repositories
- **Notary data** - Signatures and digests for images that are signed
- **Scan results** - Information about vulnerabilities in your images
- **Certificates and keys** - Certificates, public keys, and private keys that are used for mutual TLS communication

######DTR content NOT backed-up
- **Image content** - The images you push to DTR. This can be stored on the file system of the node running DTR, or other storage system, depending on the configuration. Needs to be backed up separately, depends on DTR configuration
- **Users, orgs, teams** - Create a UCP backup to back up this data
- **Vulnerability database** - Can be redownloaded after a restore

######HA

For high-availability you can deploy multiple DTR replicas, one on each UCP worker node.

######Cache deployment strategy

The main reason to use a DTR cache is so that users can pull images from a service that’s geographically closer to them.

In this example, a company has developers spread across three locations: United States, Asia, and Europe. Developers working in the US office can pull their images from DTR without problem, but developers in the Asia and Europe offices complain that it takes them a long time to pulls images.

To address that, you can deploy DTR caches in the Asia and Europe offices, so that developers working from there can pull images much faster.

######What is required to deploy DTR?
       a) A Docker Enterprise license from Docker Hub and an appropriate repository URL 
       b) Docker Enterprise Engine and Docker UCP


######Which endpoints are provided to verify DTR and UCP nodes' health?
	   a) DTR provides /_ping, /nginx_status, and /api/v0/meta/cluster_status.
	   c) DTR and UCP provide /_ping.
	   
######Which internal networks are deployed for DTR? dtr-ol

######only write access can scan

Only users with write access to a repository can manually start a scan. Users with read-only access can view the scan results, but cannot start a new scan.

The repository name needs to be unique in that namespace, can be two to 255 characters, and can only contain lowercase letters, numbers, hyphens (-), and underscores (_).

######notary {cmd} docker.io/library/
correct GUN format for naming Docker Images for Notary Client

######tag name
A tag name must be valid ASCII and may contain lowercase and uppercase letters, digits, underscores, periods and dashes. A tag name may not start with a period or a dash and may contain a maximum of 128 characters.

######install on a worker node
DTR needs to be installed on a worker node that is being managed by UCP.

######Set a tag limit

In addition to pruning policies, you can also set tag limits on repositories that you manage to restrict the number of tags on a given repository. Repository tag limits are processed in a first in first out (FIFO) manner. For example, if you set a tag limit of 2, adding a third tag would push out the first.
