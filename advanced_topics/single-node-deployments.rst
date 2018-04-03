Single-Node Deployments
=======================
When resources are scarce, or for testing purpose, KubeNow enables single-node deployments. In fact, it is possible to deploy the master node only, which will automatically enabled for service scheduling.

You can achieve this by commenting all of the lines for the other instance types (i.e. edge node, gluster node and worker node) in the ``terraform.tfvars`` file, leaving the master node only as it is shown below::

    # Master configuration (mandatory in general, above all for single-server setup)
    master_flavor = "your-master-flavor"
    master_as_edge = "true"

    # Node configuration
    # node_count = "3"
    # node_flavor = "your-node-flavor"

    # Edge configuration
    # edge_count = "2"
    # edge_flavor = "your-edge-flavor"

    # Gluster configuration
    # glusternode_count = "1"
    # glusternode_flavor = "your-glusternode-flavor"
    # glusternode_extra_disk_size = "200" # Size in GB
