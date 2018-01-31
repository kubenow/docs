Edge Nodes
==========
Edge nodes are specialized service nodes with an associated public IP address, and they act as reverse proxies, and load balancers, for the services that are exposed to the Internet. This behavior is implemented using `Traefik <https://traefik.io>`_ .

However, when IP addresses are particularly scarce, it is possible to not deploy any edge node, and to use the master node as reverse proxy instead.

Edge nodes' settings can be changed within the `terraform.tfvars` created automatically when initializing a :doc:`deploy configuration directory <../getting_started/bootstrap>` by running::

  kn init <your-provider> my_deployment

Precisely, here below can be found an example of the configuration lines to be optionally tweaked::

  # Edge configuration
  # edge_count = "2"
  # edge_flavor = "your-edge-flavor"
  
Thus it suffices to just decomment these lines and modify the above placeholders with the desired number of edge nodes and the pick a flavor among the ones available via the used provider's.