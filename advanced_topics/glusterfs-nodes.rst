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
KubeNow is configured in order to employ the Kubernetes `dynamic volume provisioning` feature. Basically it allows storage volumes to be created on-demand. Therefore this feature eliminates the need for users to pre-provision storage. See more information `here <https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/>`_.

In KubeNow we have marked GlusterFS as specific default `StorageClass` by adding the ``storageclass.kubernetes.io/is-default-class`` annotation to it (` more details <https://github.com/kubenow/KubeNow/blob/bd0562537efca511db53d82947da16391303c641/playbooks/roles/heketi-gluster/templates/storage-class.yml>`_). When a default `StorageClass` exists in a cluster and a user creates a `PersistentVolumeClaim` with `storageClassName` **unspecified** (i.e. left empty), the `DefaultStorageClass` admission controller automatically adds the `storageClassName` field pointing to the default storage class. Therefore GlusterFS is also acting as default volume provider.

Practically speaking, with KubeNow users can request dynamically provisioned storage by simply leaving the `storageClassName` field empty within their `PersistentVolumeClaim` template. Here is an example::
   
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

**NOTE**: **there can be at most one default storage class on a cluster**, or a `PersistentVolumeClaim` without `storageClassName` explicitly specified cannot be created.