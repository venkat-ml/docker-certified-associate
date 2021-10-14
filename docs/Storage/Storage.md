
Storage drivers are also called as Graph drivers.

Which driver to use depends on the context (OS and local configuration factors).

**overlay2** - recent Ubuntu, CentOS and RHEL versions

**aufs** - Ubuntu 14.04 and older

**devicemapper** - CentOS7 and earlier

Models:

- Filesystem storage - overlay2 and aufs - inefficient for heavy workloads
- Block storage - devicemapper - efficient for heavy workloads
- Object storage - Flexible and scalable


All the data of the images and containers are stored at __/var/lib/docker/__