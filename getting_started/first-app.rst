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

One simply and quick way to access the Traefik UI is to tunnel via SSH to one of the edge nodes with the following command::

    ssh -N -f -L localhost:8080:localhost:8080 ubuntu@<edge1-floating-ip>

If you are wondering how to get the ``<edge1-floating-ip>``, then open the ``inventory`` file which is created by the ``terraform apply`` command.

Using SSH tunnelling, the Traefik UI should be reachable at http://localhost:8080, and it should look something like this:

.. image:: ../img/traefik_UI_example.png

In the left side you can find your deployed frontends URLs, whereas on the right side the backend services.
