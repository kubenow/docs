Single Node Deployments
=======================
For single-server setups, it is possible to deploy the master node only, and enable it for service scheduling.

This configuration can be enabled within the `terraform.tfvars` created automatically when initializing a :doc:`deploy configuration directory <../getting_started/bootstrap>` by running::

  kn init <your-provider> my_deployment

Precisely you can achieve this by commenting all the lines for the other instance type (i.e. edge node, gluster node and worker node) but the master one as shown below::

    # Master configuration (mandatory in general, above all for single-server setup)
    master_flavor = "your-master-flavor"
    master_as_edge = "true"

    # Node configuration
    #node_count = "3"
    #node_flavor = "your-node-flavor"

    # Edge configuration
    # edge_count = "2"
    # edge_flavor = "your-edge-flavor"

    # Gluster configuration
    # glusternode_count = "1"
    # glusternode_flavor = "your-glusternode-flavor"
    # glusternode_extra_disk_size = "200" # Size in GB

    # Cloudflare configuration (optional)
    # use_cloudflare = "true"
    # cloudflare_email = "your-cloudflare-email"
    # cloudflare_token = "your-cloudflare-token"
    # cloudflare_domain = "your-domain-name"
    # cloudflare_subdomain = "your-subdomain-name"

    # Cloudflare proxy (optional)
    # cloudflare_proxied = "true"
    # cloudflare_record_texts = ["record1","record2",...] # Warn: wildcards are not supported when using proxied records