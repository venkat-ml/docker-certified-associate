######UCP

- UCP always runs with HTTPS enabled. When you connect to UCP, you need to make sure that the hostname that you use to connect is recognized by UCP’s certificates.

- If, for instance, you put UCP behind a load balancer that forwards its traffic to your UCP instance, your requests will be for the load balancer’s hostname or IP address, not UCP’s. UCP will reject these requests unless you include the load balancer’s address as a Subject Alternative Name (or SAN) in UCP’s certificates.

- If you use your own TLS certificates, make sure that they have the correct SAN values

- If you want to use the self-signed certificate that UCP has out of the box, you can set up the SANs when you install UCP with the --san argument. You can also add them after installation.

**Sign images using notary**
- To sign images in a way that UCP trusts them you need to:
    - Configure your Notary client
    - Initialize trust metadata for the repository
    - Delegate signing to the keys in your UCP client bundle

######Collections

Docker Enterprise enables controlling access to swarm resources by using collections. A collection is a grouping of swarm cluster resources that you access by specifying a directory-like path. Before grants can be implemented, collections need to be designed to group resources in a way that makes sense for an organization.

The following example shows the potential access policy of an organization. Consider an organization with two application teams, Mobile and Payments, that share cluster hardware resources, but still need to segregate access to the applications. Collections should be designed to map to the organizational structure desired, in this case the two application teams.

Collections are implemented in UCP through the use of Docker labels. All resources within a given collection are labeled with the collection, /production/mobile for instance.

Collections are groupings of objects within UCP. A collection can be made up of one or many of nodes, stacks, containers, services, volumes, networks, secrets, or configs — or it can hold other collections.

To associate a node or a stack or any resource with a collection, that resource should share the label com.docker.ucp.access.label with the collection.

A resource can be associated with zero or multiple collections, and a collection can have zero or multiple resources or other child collections in it. Collections within collections allow the structuring of resource objects in a hierarchical nature and can significantly simplify access control. Access provided at a top level collection is inherited by all its children, including any child collections.


######LDAP

Docker UCP integrates with LDAP directory services, so that you can manage users and groups from your organization’s directory and it will automatically propagate that information to UCP and DTR.


UCP services include a component called ucp-auth-api, which is the centralized service for identity and authentication used by UCP and DTR.

All UCP services are exposed using HTTPS, to ensure all communications between clients and UCP are encrypted.
By default, this is done using self-signed TLS certificates that are not trusted by client tools like web browsers. So when you try to access UCP, your browser warns that it doesn’t trust UCP or that UCP has an invalid certificate. The same happens with other client tools.

You can configure UCP to use your own TLS certificates, so that it is automatically trusted by your browser and client tools.

######Client Bundle

A client bundle contains a private and public key pair that authorizes your requests in UCP.

Using client certificate bundle on your local computer, you can use it to authenticate your requests.

What is a client bundle?

A client bundle is a group of certificates downloadable directly from the Docker Universal Control Plane (UCP) user interface within the admin section for “My Profile”.

This allows you to authorize a remote Docker engine to a specific user account managed in Docker EE, absorbing all associated RBAC controls in the process. You can now execute docker swarm commands from your remote machine that take effect on the remote cluster.

https://www.docker.com/blog/get-familiar-docker-enterprise-edition-client-bundles/


######Subjects

A subject represents a user, team, or organization. A subject is granted a role for a collection of resources. These groups of users are the same across UCP and DTR making RBAC management across the entire software pipeline uniform.

**User** - A single user or system account that an authentication backend (AD/LDAP) has validated.

**Team** - A group of users that share a set of permissions defined in the team itself. A team exists only as part of an organization, and all team members are members of the organization. A team can exist in one organization only. Assign users to one or more teams and one or more organizations.

**Organization** - The largest organizational unit in Docker Enterprise. Organizations group together teams to provide broader scope to apply access policy against.

Subjects are individual users or teams within an organization. Teams are typically backed by an LDAP/AD group or search filter. It is also possible to add users manually. But it is not possible to have a hybrid composition of users. In other words, the list of users within a team should be derived from a directory server (e.g. AD) or should be added manually, not both.

######ucp-agent

When you deploy UCP, it starts running a globally scheduled service called ucp-agent. This service monitors the node where it’s running and starts and stops UCP services, based on whether the node is a manager or a worker node.


- Manager: the ucp-agent service automatically starts serving all UCP components, including the UCP web UI and data stores used by UCP. The ucp-agent accomplishes this by deploying several containers on the node.

- Worker: on worker nodes, the ucp-agent service starts serving a proxy service that ensures only authorized users and other UCP services can run Docker commands in that node.


######Bundles

A client bundle contains a private and public key pair that authorizes your requests in UCP. In order to authenticate your requests, you can download client certificate bundle to your local computer.

UCP issues different types of certificates depending on the user:

- User certificate bundles: only allow running docker commands through a UCP manager node.
- Admin user certificate bundles: allow running docker commands on the Docker Engine of any node.

######SAN

Using external certificates is recommended when integrating with a corporate environment. 

Using external, officially-signed certificates simplifies having to distribute internal Certificate Authority (CA) certificates. 

One best practice is to use the Certificate Authority for your organization. 

Reduce the number of certificates by adding multiple Subject Alternative Names (SANs) to a single certificate. 

This allows the certificate to be valid for multiple URLs. 

For example, you can set up a certificate for ucp.example.com, dtr.example.com, and all the underlying hostnames and IP addresses. 

One certificate/key pair makes deploying certs easier.

######Docker CLI

To use the Docker CLI with UCP, download a client certificate bundle(zip) by using the UCP web UI.

######Grant

You can create by combining subject + role + resource set.

A grant defines who has how much access to what resources. Each grant is a 1:1:1 mapping of subject, role, and resource set.

For example, you can grant the “Prod Team” “Restricted Control” over services in the “/Production” collection.

######x509 issue

When you are running a docker command, Docker attempts to verify that the certificate in use is signed by UCP's certificate authority and that the domain name or IP used to connect to UCP is listed as a subject alternative name (SAN) in the UCP certificate.

If you see the following error “x509: certificate signed by unknown authority”, then Docker has not been provided with the ca certificate. To resolve this issue, use a UCP client bundle to connect to UCP from the CLI.

######Node Certificate Expiration

Universal Control Plane's management plane uses a private CA and certificates for all internal communication. The client certificates are automatically rotated on a schedule, providing a strong method for reducing the effect of a compromised node. There is an option to reduce the default time interval of 90 days to a shorter interval, however shorter intervals do add stress to the UCP cluster. To adjust the certificate rotation schedule, go to Admin -> Admin Settings -> Swarm and scroll down.


######Team's permission

Permissions are cumulative. For example, if you have Write permissions, you automatically have Read permissions:
- Read access allows users to view, search, and pull a private repository in the same way as they can a public repository.
- Write access allows users to push to repositories on Docker Hub.
- Admin access allows users to modify the repositories “Description”, “Collaborators” rights, “Public/Private” visibility, and “Delete”.

######RBAC

UCP has role-based access control (RBAC), so that you can control who can access and make changes to your cluster and applications.

Using the UI for Docker Enterprise of Docker Universal Control Plane (UCP), you authorize users to view, edit, and use cluster resources by granting role-based permissions against resource sets.

To authorize access to cluster resources across your organization, UCP administrators might take the following high-level steps:
- Add and configure subjects (users, teams, and service accounts).
- Define custom roles (or use defaults) by adding permitted operations per type of resource.
- Group cluster resources into resource sets of Swarm collections or Kubernetes namespaces.
- Create grants by combining subject + role + resource set.

######roles

The default roles in UCP are None, View Only, Restricted Control, Scheduler, and Full Control.

Each of these roles have a set of operations that define the permissions associated with the role. Additional custom roles can be defined by combining a unique set of permissions. Custom roles can be leveraged to accommodate fine-grained access control as required for certain organizations and security controls.

######Backup

Backups contain UCP configuration metadata to re-create configurations such as Administration Settings values such as LDAP and SAML, and RBAC configurations (Collections, Grants, Roles, User, and more):

The following example shows how to create a UCP backup on a manager node, encrypt it by using a passphrase, decrypt it, verify its contents, and store it locally on the node at /tmp/mybackup.tar:

```
$ docker container run \
--rm \
--log-driver none \
--name ucp \
--volume /var/run/docker.sock:/var/run/docker.sock \
--volume /tmp:/backup \
docker/ucp:3.2.6 backup \
--file mybackup.tar \
--passphrase "secret12chars" \
--include-logs=false
```

Here, the “docker/ucp” represents a docker image, “backup” is the command to execute.

#######_ping

You can use the https://<ucp-manager-url>/_ping endpoint to check the health of a single UCP manager node.

When you access this endpoint, the UCP manager validates that all its internal components are working, and returns one of the following HTTP error codes:

 - 200, if all components are healthy

 - 500, if one or more components are not healthy
 
######SSO with UCP

To only authenticate once, you can configure DTR to have single sign-on (SSO) with UCP.

Users are shared between UCP and DTR by default, but the applications have separate browser-based interfaces which require authentication.

When installing DTR, pass --dtr-external-url <url> to enable SSO.

You can also enable single sign-on from the command line by reconfiguring your DTR.

To do so, run the following:
```
docker run --rm -it \
docker/dtr:2.7.6 reconfigure \
--dtr-external-url dtr.example.com \ 
…
``` 

######Which role should be set in UCP for the DBA team to allow them to create their own volumes? View Only

If the type of orchestrator is changed, the existing workload will not migrate to the new orchestrator automatically.

```
docker node update \ --label-add com.docker.ucp.orchestrator.swarm=true
```

Ucp-agent - core component of UCP

ucp-dsinfo - Docker system information collection script to assist with troubleshooting

TCP 179 port for managers, workers

######commands is used to access audit logs of Universal Control Plane
```
docker logs ucp-controller
```

