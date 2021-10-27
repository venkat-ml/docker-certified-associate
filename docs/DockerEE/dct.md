
######Docker Content trust

When communicating over an untrusted medium such as the internet, it is critical to ensure the integrity and the publisher of all the data a system operates on. You use the Docker Engine to push and pull images (data) to a public or private registry. Content trust gives you the ability to verify both the integrity and the publisher of all the data received from a registry over any channel.

Docker Content Trust (DCT) provides the **ability to use digital signatures** for data sent to and received from remote Docker registries. These signatures allow client-side or runtime verification of the integrity and publisher of specific image tags.

Docker Content Trust (DCT) provides the ability to use digital signatures for data sent to and received from remote Docker registries. These signatures allow client-side or runtime verification of the integrity and publisher of specific image tags.
https://docs.docker.com/engine/security/trust/content_trust/#about-docker-content-trust-dct

Docker Content Trust Signature Verification

The Docker Engine can be configured to only run signed images. The Docker Content Trust signature verification feature is built directly into the dockerd binary.

This is configured in the Dockerd configuration file.

To enable this feature, trustpinning can be configured in daemon.json, whereby only repositories signed with a user-specified root key can be pulled and run.

######Which of these keys requires a passphrase to unlock it while signing images?
    a) Target
    b) Snapshot
    
######How can we ensure that a specific image is deployed in production?
	a) By using the image's hash for deploying containers.
    b) Signing images will ensure their tagging and provenance.


######Docker Content Trust Keys

Trust for an image tag is managed through the use of signing keys. A key set is created when an operation using DCT is first invoked. A key set consists of the following classes of keys:

- an offline key that is the root of DCT for an image tag
- repository or tagging keys that sign tags
- server-managed keys such as the timestamp key, which provides freshness security guarantees for your repository

######Delegations for content trust

Delegations in Docker Content Trust (DCT) allow you to control who can and cannot sign an image tag. 

A delegation will have a pair of private and public delegation keys. 

A delegation could contain multiple pairs of keys and contributors in order to 

    a) allow multiple users to be part of a delegation, and 
    
    b) to support key rotation.
    