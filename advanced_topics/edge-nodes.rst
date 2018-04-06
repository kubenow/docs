Edge Nodes
==========
Edge nodes are specialized service nodes with an associated public IP address, and they run `Traefik <https://traefik.io>`_ acting as reverse proxies, and load balancers, for the services that are exposed to the Internet. In the default settings, we don't deploy any edge node enabling the reverse proxy logic in the master node instead. However, in production settings we recommend to deploy one or more edge nodes to reduce the load in the master.

To deploy edge nodes, it is sufficient to uncomment the following lines in the ``config.tfvars`` file, and to set the desired number of edge nodes, along with an available instance flavor::

  # Master configuration (mandatory in general, above all for single-server setup)
  master_flavor = "your-master-flavor"
  master_as_edge = "false"

  # Edge configuration
  edge_count = "2"
  edge_flavor = "your-edge-flavor"

Please notice that we set ``master_as_edge = "false"`` to disable Traefik in the master node.
