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

Missing DomainID or DomainName to authenticate by Username
----------------------------------------------------------
When running terraform and/or packer you may be prompted with the following error::

    You must provide exactly one of DomainID or DomainName to authenticate by Username
    
If this is the case, then setting either ``OS_DOMAIN_ID`` or ``OS_DOMAIN_NAME`` in your environment should fix the issue. For further information, please refer to this document: https://www.terraform.io/docs/providers/openstack/index.html.
