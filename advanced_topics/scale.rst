Manual Cluster Scaling
======================
KubeNow is now featuring the ability to add or delete nodes to your cluster with the help of the command ``kn scale``.

While your cluster is deployed and running, it is necessary first to update the number of nodes in the ``config.tfvars`` file created by the very first command ``kn init`` as explained `here <../getting_started/bootstrap.html>`_ .
Currently ``kn-scale`` is only supporting adding and removing `worker nodes` or `edge nodes` (not `master nodes`). Last but not least, it is important to have at least one ``node`` left with enough space for your running services.

Once the ``config.tfvars`` has been updated with the new desired number of nodes, then we simply run the command::

  kn scale
  
What will happen depends on whether we are downscaling or upscaling.

In the former scenario the command will first drain resources out of one or more node/s by executing ``kubectl drain``, then remove it/them via ``kubectl delete node`` and finally a call to ``terraform apply`` will shut it/them down.

In the latter case one or more new node/s is/are first created by executing ``terraform apply`` and then joined to the already deployed cluster via ``kubeadm join`` by using our default bootstrap script (more details of such script in the main `KubeNow's GitHub repository <https://github.com/kubenow/KubeNow/blob/master/bootstrap/bootstrap-default.sh>`_ ). 
