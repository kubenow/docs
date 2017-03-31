OpenStack troubleshooting
=========================

.. contents::

Console logs on OpenStack
-------------------------

Can't get the status from the nodes with ``ansible master -a "kubectl get nodes"``? The nodes might not have started all right. Checking the console logs with nova could help.

List node IDs, floating IPs etc.::

  nova list

Show console output from node of interest::

  nova console-log <node-id>

Terraform and/or Packer Bbootstrap error
----------------------------------------
The OpenStack provider is used to interact with the many resources supported by OpenStack. The provider needs to be configured with the proper credentials before it can be used.

During the bootstrap steps, when applying the terraform and/or packer commands you may be prompted with the following error::

    You must provide exactly one of DomainID or DomainName to authenticate by Username
    
If this is the case, then you need to set either ``OS_DOMAIN_ID`` or ``OS_DOMAIN_NAME`` in your OpenStack environment.