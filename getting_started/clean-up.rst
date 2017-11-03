Clean after yourself
--------------------

Cloud resources are typically pay-per-use, hence it is good to release them when they are not used. Here we show how to destroy a KubeNow cluster.

To release the resources, please run::

  kn destroy

**Warning:** if you delete the cluster configuration directory (``my_deployment``) the cluster status will be lost, and you'll have to delete the resources manually.
