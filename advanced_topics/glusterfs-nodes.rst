GlusterFS Nodes
===============
GlusterFS nodes are specialized service nodes. They run only `GlusterFS <https://www.gluster.org>`_ and they are attached to a block storage volume to provide additional capacity. In the default settings, we don't deploy GlusterFS nodes, as it is not required in many use cases. However, GlusterFS can be particularly convenient when a distributed file system is needed for container synchronization.

To deploy GlusterFS nodes, it is sufficient to uncomment the following lines in the ``config.tfvars`` file, and to set the desired number of edge nodes, along with an available instance flavor and block storage disk size::

 # Gluster configuration
 glusternode_count = "1"
 glusternode_flavor = "your-glusternode-flavor"
 glusternode_extra_disk_size = "200" # Size in GB

How to claim a GlusterFS volume
-------------------------------
KubeNow is configured to employ the Kubernetes `dynamic volume provisioning <https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/>`_, enabling GlusterFS storage volumes to be created on-demand. In addition, GlusterFS is configured as default StorageClass, meaning that when a user creates a PersistentVolumeClaim with unspecified ``storageClassName`` (i.e. left empty), the `DefaultStorageClass` admission controller automatically adds the GlusterFS `storageClassName`.

In practice, users can request GlusterFS dynamically provisioned storage by simply leaving the `storageClassName` field empty within their `PersistentVolumeClaim` template. An example follows::

 apiVersion: v1
 kind: PersistentVolumeClaim
 metadata:
  name: name-you-choose
  spec:
    accessModes:
      - ReadWriteOnce
    storageClassName: # left empty
    resources:
      requests:
        storage: 1Gi
