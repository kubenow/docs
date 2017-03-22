Deploy your first application
=============================

In this guide we are going to deploy a simple application: `cheese-deployent <https://github.com/mcapuccini/KubeNow/blob/master/examples/cheese-deployment.yml>`_. This deployment defines 3 services with a 2 replication factor. Traefik will load balance the requests among the replicas in the Kubernetes nodes. For more details about the cheese deployment, please refer to: https://docs.traefik.io/user-guide/kubernetes.

Start by substituting ``domain_name`` with ``yourdomain.com`` in ``cheese-deployent.yml`` (where `yourdomain.com` is the domain that points to the edge nodes, through CloudFlare)::

  sed -i -e 's/domain_name/yourdomain.com/g' examples/cheese-deployment.yml

Now, copy the ``cheese-deployent.yml`` file into the master node::

  ansible master -m copy -a "src=examples/cheese-deployment.yml dest=/home/ubuntu"

Finally, deploy the application using kubectl::

  ansible master -a "kubectl apply -f /home/ubuntu/cheese-deployment.yml"

If everything goes well you should see some front-ends and back-ends showing up in the Traefik UI, and you should be able to access the services at:

- http://stilton.yourdomain.com
- http://cheddar.yourdomain.com
- http://wensleydale.yourdomain.com

One simply and quick way to access the Traefik UI is to tunneling via ssh to one of the edge nodes with the following command::

    ssh -N -f -L localhost:808x:localhost:808x ubuntu@<edge-floating-ip>

If you are wondering how to get the `edge-floating-ip`, then open the `inventory` file which is created after having spun up the cluster in the KubeNow folder

By visitng your localhost:808x in your browser you should be displayed with the following UI:

.. image:: ../img/traefik_UI_example.png

where on the left highlighted in yellow you can find your deployed frontend apps whereas on the right highlighted in green the backend services are listed 
