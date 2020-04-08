# K8S NFS-Client Provisioner

nfs-client-provisioner, as presented by kubernetes-incubator, is an automatic provisioner that use your existing and already configured NFS server to support dynamic provisioning of Kubernetes Persistent Volumes via Persistent Volume Claims. 

The original version of this tool provisions persistent volumes dynamically with the following name:

``${namespace}-${pvcName}-${pvName}``. 

## Available options

The nfs-client-provisioner is a go binary that offers two functions:
- Create: The function creates the PV with the proprer spec, and the data directory named as follow:`${namespace}-${pvcName}-${pvName}``.
- Delete: If reclaim policy is `Delete`, then this function will be called to delete the PV along side with the mounted volume. If `archiveOnDelete` is set to `true`, the data directory will be renamed as follow: ``archived-${original_name}``.

## What are the limits ?

When moving on with the tests of different use cases using this provisioner, we had the following problems:

- The created data directory is not located exactly where we want it to be. It is always created under another directory named ``${namespace}-${pvcName}-${pvName}``.
- For production measures, we don't use Delete or Recycle reclaim policies, but we would like the PV to be deleted when PVC is no longer needed.
- For the same measures, we like to keep our data directory even after deleting the PV and PVC.

### Side effects of these issues

I will give two different use cases to show you the purpose of my work:

1- Data directory
   - 

The current version had these issues. And the idea of having the data directory under another randomly named directory is not appreciated especially when we have more than 300 deployments running in production conditions.

## How to fix them ?

- Make sure the mounted directory has the same name value set in the subPath field on your application deployment spec.
- Make sure that, when PVC is deleted, PV is deleted as well, but the mounted data directory is still available for later use.

Kubernetes NFS client provisioner that makes sure new PVs keeps connecting to the right volumes.
The nfs-client-provisioner presented by kubernetes-incubator under the repository external-storage
