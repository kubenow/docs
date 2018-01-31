GlusterFS Nodes
===============
Storage nodes are specialized nodes too. They run **only** the shared distributed file system, and they are attached to a block storage volume to provide additional capacity. The shared file system is implemented by `GlusterFS <https://www.gluster.org>`_ and `Heketi <https://github.com/heketi/heketi>`_ .
The two frameworks enable user applications for provisioning shared volumes seamlessly, trough the standard `Kubernetes API <https://kubernetes.io/>`_ .

GlusterFS nodes settings can be changed within the `terraform.tfvars` created automatically when initializing a :doc:`deploy configuration directory <../getting_started/bootstrap>` by running::

  kn init <your-provider> my_deployment

Precisely, here below can be found an example of the configuration lines to be tweaked optionally if need be::

 # Gluster configuration
 # glusternode_count = "1"
 # glusternode_flavor = "your-glusternode-flavor"
 # glusternode_extra_disk_size = "200" # Size in GB

As you may notice, these lines are commented by default thus leaving a user freedom of choosing custom solutions for a shared distributed file system if need be (which most of the time is actually the case for distributed microservice-oriented applications).