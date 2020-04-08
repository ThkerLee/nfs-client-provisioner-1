# K8S NFS-Client Provisioner

nfs-client-provisioner, as presented by kubernetes-incubator, is an automatic provisioner that use your existing and already configured NFS server to support dynamic provisioning of Kubernetes Persistent Volumes via Persistent Volume Claims. 

The original version of this tool provisions persistent volumes dynamically with the following name:

``${namespace}-${pvcName}-${pvName}``. 

# What is wrong with that ?

When moving on with testing different use cases using this provisioner, we had the following problems:

- The created data directory is not located exactly where we want it to be. It is always created under another directory named `${namespace}-${pvcName}-${pvName}``.
- For production measures, we don't use Delete or Recycle reclaim policies, but we would like the PV to be deleted when PVC is no longer needed.
- For the same measures, we like to keep our data directory even after deleting the PV and PVC.

The current version had these issues. And the idea of having the data directory under another randomly named directory is not appreciated especially when we have more than 300 deployments running in production conditions.

# What is the solution ?


Kubernetes NFS client provisioner that makes sure new PVs keeps connecting to the right volumes.
The nfs-client-provisioner presented by kubernetes-incubator under the repository external-storage
