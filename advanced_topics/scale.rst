Scale
======================
You can add or delete nodes to your cluster with help of command ``kn scale``.
First you need to update the number of nodes in your ``config.tfvars`` file.
Currently ``kn-scale`` is only supporting adding and removing `nodes` or `edges` (not `masters`).
You need to have at least one ``node`` left with enough space for your services.

After you have updated your ``config.tfvars`` then you run command::

  kn scale
  

If downscaling then the command will first drain resources of nodes by executing ´kubectl drain` and `kubectl delete node` before a call to `terraform apply` will shut them down.

If upscaling new nodes are created by `terraform apply` and joined to the cluster via `kubeadm join` in the default bootstrap script. 
