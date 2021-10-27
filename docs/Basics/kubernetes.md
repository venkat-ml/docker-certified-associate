
Kubernetes supports two primary modes of finding a Service, environment variables(injected by the kubelet when pods are created) and DNS. 

Both are valid options, however, the most common method to discover and reach a service from within Docker Kubernetes Service is to use the embedded DNS service.

https://kubernetes.io/docs/concepts/services-networking/service/#discovering-services

######ClusterIP vs Nodeport vs Ingress

Services will receive a cluster-scoped Virtual IP address also known as ClusterIP.

ClusterIP is the default method of exposing the service internally. Once the service is created and a VIP is associated with it, every kube-proxy running on every cluster node will program an iptables rule so that all traffic destined to that VIP will be redirected to one of the Service’s backend pods instead.

If the service needs to be accessed from outside the cluster, then there are a couple of available options to doing so. Mainly NodePort and LoadBalancer.

Ingress is another method that you can use. Ingress is technically not a Type of a service, but it's another method for using Layer 7 based routing to expose your services externally.

https://success.docker.com/article/ucp-service-discovery-k8s

**Calico**

Docker Universal Control Plane (UCP) uses Calico as the default Kubernetes networking solution. 

Calico is configured to create a Border Gateway Protocol (BGP) mesh between all nodes in the cluster.

######PersistentVolume

A PersistentVolume (PV) is a piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes.

A PV can have a class, which is specified by setting the storageClassName attribute to the name of a StorageClass. A PV of a particular class can only be bound to PVCs requesting that class. A PV with no storageClassName has no class and can only be bound to PVCs that request no particular class.

https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistent-volumes

######Dynamic volume provisioning

Dynamic volume provisioning allows storage volumes to be created on-demand. Without dynamic provisioning, cluster administrators have to manually make calls to their cloud or storage provider to create new storage volumes, and then create PersistentVolume objects to represent them in Kubernetes. The dynamic provisioning feature eliminates the need for cluster administrators to pre-provision storage. Instead, it automatically provisions storage when it is requested by users.

https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/#enabling-dynamic-provisioning

#######Claims

Claims will remain unbound indefinitely if a matching volume does not exist. Claims will be bound as matching volumes become available. For example, a cluster provisioned with many 50Gi PVs would not match a PVC requesting 100Gi. The PVC can be bound when a 100Gi PV is added to the cluster.

#######PVC to PV - one-to-one mapping

A user creates, or in the case of dynamic provisioning, has already created, a PersistentVolumeClaim with a specific amount of storage requested and with certain access modes. A control loop in the master watches for new PVCs, finds a matching PV (if possible), and binds them together. If a PV was dynamically provisioned for a new PVC, the loop will always bind that PV to the PVC. Otherwise, the user will always get at least what they asked for, but the volume may be in excess of what was requested. Once bound, PersistentVolumeClaim binds are exclusive, regardless of how they were bound. A PVC to PV binding is a one-to-one mapping, using a ClaimRef which is a bi-directional binding between the PersistentVolume and the PersistentVolumeClaim.

######Which concept is responsible for managing internal load balancing for Kubernetes? - ClusterIP

######Which labels are required to deploy a service with Interlock? Com.docker.interlock.hosts

######How can we publish services externally in Kubernetes? Nodeport, Ingress controllers

######Which Kubernetes resources provide an application's resilience? Deployment, Replicaset

######Which sentences are true about Docker Swarm and Kubernetes networking? 
	Mutual TLS communications ensure control-plane security in Docker Swarm.
    Kubernetes uses certificates to ensure security for user access and the internal control plane.
    
Role grants - RoleBinding and ClusterRoleBinding

Role, subject, resource, namespace - Kubernetes grant

######commands is used to schedule kubernetes workloads on a node
```
docker node update \ --label-add com.docker.ucp.orchestrator.kubernetes=true
```
    






