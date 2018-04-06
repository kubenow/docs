GlusterFS Nodes
===============
GlusterFS nodes are specialized service nodes. They run only `GlusterFS <https://www.gluster.org>`_ and they are attached to a block storage volume to provide additional capacity. In the default settings, we don't deploy GlusterFS nodes, as it is not required in many use cases. However, GlusterFS can be particularly convenient when a distributed file system is needed for container synchronization.

To deploy GlusterFS nodes, it is sufficient to uncomment the following lines in the ``config.tfvars`` file, and to set the desired number of edge nodes, along with an available instance flavor and block storage disk size::

 # Gluster configuration
 glusternode_count = "1"
 glusternode_flavor = "your-glusternode-flavor"
 glusternode_extra_disk_size = "200" # Size in GB
