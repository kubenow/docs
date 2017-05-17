Install core components
=======================
At this point you should have a core Kubernetes cluster up and running, on your cloud provider. The Kubernetes cluster that you deploy in the previous step is not ready to use, as it lacks the overlay network service that will allow your containers to communicate. KubeNow comes with an `install-core.yml <https://github.com/kubenow/KubeNow/blob/master/playbooks/install-core.yml>`_ playbook that installs the overlay network along with some other useful components.

The core components include:

- `Weave <http://weave.works>`_ overlay network (tag: `minimal`)
- `Traefik <http://traefik.io/>`_ HTTP reverse proxy and load balancer (**only on the edge nodes and master**, tag: ``traefik``)
- `GlusterFS <https://www.gluster.org/>`_ GlusterFS distributed files system (**on every node but the master and the edge**, tag: ``glusterfs``).

  - The playbook also configures a `persistent volume <https://kubernetes.io/docs/user-guide/persistent-volumes/>`_ called ``shared-volume`` that points to the GlusterFS endpoints.

**Pro tip:** you can use the tags to install or skip certain components while running `install-core.yml <https://github.com/kubenow/KubeNow/blob/master/playbooks/install-core.yml>`_. For more informations please refer to the `Ansible tags documentation <http://docs.ansible.com/ansible/playbooks_tags.html>`_.

.. image:: ../img/architecture.png

This kind of deployment is particularly convenient, as only the master node, and the edge nodes (that run `Traefik`_) need to be associated to public IPs (which can be scarce). Therefore, the end user will access the microservices running in the Kubernetes nodes, through an edge node that will act as a reverse proxy. The `Cloudflare <http://cloudflare.com>`_ service will loadbalance the requests over the edge nodes.

Deploy the stack
----------------
Once you are done with the Cloudflare account configuration, you can deploy the stack via `Ansible <http://ansible.com>`_::

  ansible-playbook playbooks/install-core.yml

To make sure that each service is running you can run the following command::

  ansible-playbook playbooks/infra-test.yml

It may happen that the test will return the following error::

    FAILED! => {"failed": true, "msg": "An unhandled exception occurred while running 
    the lookup plugin 'dig'. Error was a <class 'ansible.errors.AnsibleError'>, original message: 
    Can't LOOKUP(dig): module dns.resolver is not installed"}

If this is the case, then as it can be deduced by the error message, the dns python module is missing. Installing the module ``dnspython`` via ``pip`` (i.e. ``pip install dnspython``) will fix the glitch and allow the test to perform correctly. See the issue reported in the `Ansible GitHub discussion <https://github.com/ansible/ansible/issues/19719>`_ for in depth details.
