Ingress Port Opening
====================
On some deployments you might need to manually configure ports in order to allow specific service traffic. This can be done by adding/modifying the field ``ports_ingress_tcp`` in the configuration file ``config.tfvars`` (which you should be familiar with by now). Default is equals to ``["22", "80", "443"]``. Let's suppose your newly deployed service also requires ports ``7443`` and ``9443`` to be opened, then one would modify the ``config.tfvars`` as follows::

 # Cluster configuration
 ....

 ports_ingress_tcp = ["22", "80", "443", "7443", "9443"]

 ....

Hence we've modified the field ``ports_ingress_tcp`` so to read **ports_ingress_tcp = ["22", "80", "443", 7443", "9443"]**.

To Keep in Mind
~~~~~~~~~~~~~~~
It is important to consider potential security risks to avoid future issues. While opening ports does put you more at risk than having none open, you are only in danger if an attack can exploit the service that is using that port. A port is not an all access pass to a cluster/network if an attacker happens upon it. Security is a complex topic indeed and can vary from case to case. Nevertheless here are some best practices for a proper configuration:

- **Block by default**: block all traffic by default and explicitly allow only specific traffic to known services. This strategy provides good control over the traffic and reduces the possibility of a breach because of service misconfiguration.

- **Allow specific traffic**:in general the rules that you use to define network access should be as specific as possible. This strategy is referred to as *the principle of least privilege*, and it forces control over network traffic. In this case what you are specifying is a certain port (or list of them) for your services to be reachable from outside the cluster's network.
