Bootstrap Kubernetes on a host cloud
====================================

This step is slightly different for each host cloud. Here you find a section for each of the supported providers.

.. contents:: Sections
  :depth: 2

Bootstrap on OpenStack
----------------------

Prerequisites
~~~~~~~~~~~~~

In this section we assume that:

- You have downloaded and sourced the OpenStack RC file for your tenancy: ``source project-openrc.sh`` https://docs.openstack.org/user-guide/common/cli-set-environment-variables-using-openstack-rc.html#download-and-source-the-openstack-rc-file
- You have a floating IP quota that allows to allocate a public IP for each master and edge node (at least 1 in total)
- You installed the glance command-line client in your local machine: https://docs.openstack.org/user-guide/common/cli-install-openstack-command-line-clients.html

Import the KubeNow image (only the first time you are deploying)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The first time you are going to deploy KubeNow, you'll have to import its cloud image. This considerably speeds up the following bootstraps, as all of the required software will already be installed on the instances.

You can import the latest image build by running the following `Ansible <http://ansible.com>`_ playbook::

  ansible-playbook playbooks/import-openstack-image.yml

If everything goes well, you will see the new image in the OpenStack web interface (Compute > Images). As an alternative, you can check that the image is present using the OpenStack command line client::

  glance image-list

Bootstrap Kubernetes
~~~~~~~~~~~~~~~~~~~~

Now we are going to provision the required virtual infrastructure in OpenStack using Terraform. This procedure will inject enough information in each instance, to independently provision itself.

Start by creating a ``terraform.tfvars`` file. There is a template that you can use for your convenience: ``mv terraform.tfvars.os-template terraform.tfvars``. In this configuration file you will need to set:

**Cluster configuration**

- **cluster_prefix**: every resource in your tenancy will be named with this prefix
- **kubenow_image**: name of the image that you previously imported using Ansible
- **ssh_key**: path to your public ssh-key to be used (for ssh node access)
- **floating_ip_pool**: a floating IP pool label
- **external_network_uuid**: the uuid of the external network in the OpenStack tenancy
- **dns_nameservers**: (optional, only needed if you want to use other dns-servers than default 8.8.8.8 and 8.8.4.4)
- **kubeadm_token**: a token that will be used by kubeadm, to bootstrap Kubernetes. You can run generate_kubetoken.sh to create a valid one.

If you are wondering where you can correctly get a `floating_ip_pool` and an `external_network_uuid`, then one way is to inquiry your OpenStack settings. You can easily and quickly achieve this by installing one of the OpenStack command-line interface called `nova` . Installation occurs via ``pip``, however please refer to the offical `OpenStack documentation <https://docs.openstack.org/user-guide/common/cli-install-openstack-command-line-clients.html>`_ in depth details.

Once `nova` is installed, run the following command::
    
    nova network-list

Depending on your tenancy settings you should get a similar output::

    +--------------------------------------+----------------+------+
    | ID                                   | Label          | Cidr |
    +--------------------------------------+----------------+------+
    | 5f274562-89b6-4ab2-a18f-94b159b0b85d | internal       | -    |
    | d9384930-baa5-422b-8657-1d42fb54f89c | net_external   | -    |
    +--------------------------------------+----------------+------+

Thus in this specific case the above mentioned fields will be set as following::

    floating_ip_pool: net_external
    external_network_uuid: d9384930-baa5-422b-8657-1d42fb54f89c

**Master configuration**

- **master_flavor**: an instance flavor for the master
- **master_act_as_edge**: master is acting as gateway for accessing services

**Node configuration**

- **node_count**: number of Kubernetes nodes to be created (no floating IP is needed for these nodes)
- **node_flavor**: an instance flavor name for the Kubernetes nodes
- **node_flavor_id**: this is an alternative way of specifying the instance flavor (optional, please set this only if **node_flavor** is empty)

**Edge configuration**

In KubeNow setup, the edge nodes (and in default setup also the the master), act as gateways with public ip:s to give access to the services running in the Kubernetes nodes. Edge nodes are ordinary worker nodes that also have an "edge" label and therefore kubernetes will run a gateway pod on these nodes. If you just do an informal deployment and don't need to load balance your service requests, the default setup with only master node acting as a gateway might be sufficient. Otherwise you could balance your service requests on 2-3 edge nodes.

- **edge_count**: number of egde nodes to be created
- **edge_flavor**: an instance flavor for the edge nodes

If you are wondering yet again where you can fetch correct flavor label names then no worries, you are not being a stranger here. The nova command-line interface will come in handy. Just run the following command::

    nova flavor-list
    
Depending on your tenancy settings you should get a similar output::

    +--------+------------+-----------+------+-----------+------+-------+-------------+----------+
    | ID     | Name       | Memory_MB | Disk | Ephemeral | Swap | VCPUs | RXTX_Factor | Is_Public|
    +--------+------------+-----------+------+-----------+------+-------+-------------+----------+
    | 8c7ef1 | ssc.tiny   | 512       | 1    | 0         |      | 1     | 1.0         | True     |
    | 8d7ef2 | ssc.small  | 2048      | 20   | 0         |      | 1     | 1.0         | True     |
    | 8e7ef3 | ssc.medium | 4096      | 40   | 0         |      | 2     | 1.0         | True     |
    | 8f7ef4 | ssc.large  | 8192      | 80   | 0         |      | 4     | 1.0         | True     |
    | 8g7ef5 | ssc.xlarge | 16384     | 160  | 0         |      | 8     | 1.0         | True     |
    +--------+------------+-----------+------+-----------+------+-------+-------------+----------+

Based how many resources your applications require, then you may want to select the nodes' flavor accordingly. E.g.::

    master_flavor: ssc.medium
    edge_flavor:   ssc.medium
    node_flavor:   ssc.large
    
**Access configuration (optional)**

Typically, you want the end user to access your services through a domain name (e.g. ``servicename.mydomain.com``). One option is to manually configure the DNS services, for a domain name and to load balance the requests among the edge nodes. However, doing this for each deployment can be tedious, and prone to configuration errors. If you just do an informal deployment and don't have a domain name to be used, KubeNow is automatically configuring your setup with the free `nip.io service <http://nip.io/>`_. Otherwise, we recommend to sign up for a free account on `Cloudflare <https://www.cloudflare.com>`_, that you can use as dynamic DNS service for your domain name.

- **use_cloudflare**: set to true if you want KubeNow to configure your cloudflare settings during deployment (if set to false KubeNow will use the free nip.io service instead)
- **cloudflare_email**: the mail that you used to register your Cloudflare account
- **cloudflare_token**: an authentication token that you can generate from the Cloudflare web interface
- **cloudflare_domain**: a zone that you created in your Cloudflare account. This typically matches your domain name (e.g. somedomain.com)    

Once you are done with your settings you are ready to bootstrap the cluster using Terraform::

  terraform get openstack # get required modules (only the first time you deploy)
  terraform apply openstack # deploy the cluster

While waiting for the bootstrap process to complete, it is important to avoid to abruptingly stop it (e.g by pressing ``Ctrl+C`` in the console), otherwise chances are that your local file `terraform.tfstate <https://www.terraform.io/docs/state/>`_ will likely be corrupted which can lead to technical complication in successfully deploying the cluster.

If everything goes well, something like the following message will be printed::

  Apply complete! Resources: X added, 0 changed, 0 destroyed.

To verify that each node connected to the master you can run::

  ansible master -a "kubectl get nodes"

If all of the nodes are not yet connected and in the Ready state, wait a minute and try again. Keep in mind that booting the instances takes a couple of minutes.

Good! Now you have a minimal Kubernetes cluster up and running, and you are ready to :doc:`install the KubeNow core components <install-core>`.


Bootstrap on Google Cloud (GCE)
-------------------------------

Prerequisites
~~~~~~~~~~~~~

In this section we assume that:

- You have enabled the Google Compute Engine API: API Manager > Library > Compute Engine API > Enable
- You have created and downloaded a service account file for your GCE project: Api manager > Credentials > Create credentials > Service account key

Import the KubeNow image (only the first time you are deploying)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The first time you are going to deploy KubeNow, you'll have to import its cloud image. This considerably speeds up the following bootstraps, as all of the required software will already be installed on the instances.

You can import the latest image build by running the following `Ansible <http://ansible.com>`_ playbook::

  ansible-playbook -e "credentials_file_path=/full/path/to/service_account.json" playbooks/import-gce-image.yml

If everything goes well, you will see the new image in the GCE web interface (Compute Engine > Images). As an alternative, you can check that the image is present using the Google Cloud command line client::

  gcloud compute images list

Bootstrap Kubernetes
~~~~~~~~~~~~~~~~~~~~

Now we are going to provision the required virtual infrastructure in Google Cloud using Terraform. This procedure will inject enough information in each instance, to independently provision itself.

Start by creating a ``terraform.tfvars`` file. There is a template that you can use for your convenience: ``mv terraform.tfvars.gce-template terraform.tfvars``. In this configuration file you will need to set:

**Cluster configuration**

- **cluster_prefix**: every resource in your project will be named with this prefix (the name must match ``(?:[a-z](?:[-a-z0-9]{0,61}[a-z0-9])?)``, e.g. "kubenow-image")
- **kubenow_image**: name of the image that you imported using Ansible
- **kubeadm_token**: a token that will be used by kubeadm, to bootstrap Kubernetes. You can run `generate_kubetoken.sh` to create a valid one.
- **ssh_key**: path to your public ssh-key to be used (for ssh node access)

**Google credentials**

- **gce_credentials_file**: path to your service account file
- **gce_region**: the zone for your project (e.g. ``europe-west1-b``)
- **gce_project**: your project id

**Master configuration**

- **master_flavor**: an instance flavor for the master (e.g. ``n1-standard-1``)
- **master_disk_size**: master disk size in GB
- **master_act_as_edge**: master is acting as gateway for accessing services

**Node configuration**

- **node_count**: number of Kubernetes nodes to be created
- **node_flavor**: an instance flavor for the Kubernetes nodes (e.g. ``n1-standard-1``)
- **node_disk_size**: nodes disk size in GB

**Edge configuration**

In KubeNow setup, the edge nodes (and in default setup also the the master), act as gateways with public ip:s to give access to the services running in the Kubernetes nodes. Edge nodes are ordinary worker nodes that also have an "edge" label and therefore kubernetes will run a gateway pod on these nodes. If you just do an informal deployment and don't need to load balance your service requests, the default setup with only master node acting as a gateway might be sufficient. Otherwise you could balance your service requests on 2-3 edge nodes.

- **edge_count**: number of egde nodes to be created
- **edge_flavor**: an instance flavor for the edge nodes (e.g. ``n1-standard-1``)
- **edge_disk_size**: edges disk size in GB

**Access configuration (optional)**

Typically, you want the end user to access your services through a domain name (e.g. ``servicename.mydomain.com``). One option is to manually configure the DNS services, for a domain name and to load balance the requests among the edge nodes. However, doing this for each deployment can be tedious, and prone to configuration errors. If you just do an informal deployment and don't have a domain name to be used, KubeNow is automatically configuring your setup with the free `nip.io service <http://nip.io/>`_. Otherwise, we recommend to sign up for a free account on `Cloudflare <https://www.cloudflare.com>`_, that you can use as dynamic DNS service for your domain name.

- **use_cloudflare**: set to true if you want KubeNow to configure your cloudflare settings during deployment (if set to false KubeNow will use the free nip.io service instead)
- **cloudflare_email**: the mail that you used to register your Cloudflare account
- **cloudflare_token**: an authentication token that you can generate from the Cloudflare web interface
- **cloudflare_domain**: a zone that you created in your Cloudflare account. This typically matches your domain name (e.g. somedomain.com)

Once you are done with your settings you are ready to bootstrap the cluster using Terraform::

  terraform get gce # get required modules (only the first time you deploy)
  terraform apply gce # deploy the cluster

If everything goes well, something like the following message will be printed::

  Apply complete! Resources: X added, 0 changed, 0 destroyed.

To verify that each node connected to the master you can run::

  ansible master -a "kubectl get nodes"

If all of the nodes are not yet connected and in the Ready state, wait a minute and try again. Keep in mind that booting the instances takes a couple of minutes.

Good! Now you have a minimal Kubernetes cluster up and running, and you are ready to :doc:`install the KubeNow core components <install-core>`.

Bootstrap on Amazon Web Services (EC2)
--------------------------------------

Prerequisites
~~~~~~~~~~~~~

In this section we assume that:

- You have an IAM user along with its *access key* and *security credentials* (http://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html)

Bootstrap Kubernetes
~~~~~~~~~~~~~~~~~~~~

Now we are going to provision the required virtual infrastructure in AWS (Amazon Web Services) using Terraform. This procedure will inject enough information in each instance, to independently provision itself.

Start by creating a ``terraform.tfvars`` file. There is a template that you can use for your convenience: ``mv terraform.tfvars.aws-template terraform.tfvars``. In this configuration file you will need to set:

**Cluster configuration**

- **cluster_prefix**: every resource in your tenancy will be named with this prefix
- **kubenow_image**: name of the KubeNow image that you want to use
- **kubeadm_token**: a token that will be used by kubeadm, to bootstrap Kubernetes. You can run `generate_kubetoken.sh` to create a valid one.
- **ssh_key**: path to your public ssh-key to be used for ssh node access (e.g. ``~/.ssh/id_rsa.pub``)
- **aws_region**: the region where your cluster will be bootstrapped (e.g. ``eu-west-1``)

  + **Warning:** the image that you previously selected has to be available in this region
  
- **availability_zone**: an availability zone for your cluster (e.g. ``eu-west-1a``)

**Credentials**

- **aws_access_key_id**: your access key id
- **aws_secret_access_key**: your secret access key

**Master configuration**

- **master_instance_type**: an instance type for the master (e.g. ``t2.medium``)
- **master_disk_size**: master disk size in GB
- **master_act_as_edge**: master is acting as gateway for accessing services

**Node configuration**

- **node_count**: number of Kubernetes nodes to be created
- **node_instance_type**: an instance type for the Kubernetes nodes (e.g. ``t2.medium``)
- **node_disk_size**: node disk size in GB

**Edge configuration**

In KubeNow setup, the edge nodes (and in default setup also the the master), act as gateways with public ip:s to give access to the services running in the Kubernetes nodes. Edge nodes are ordinary worker nodes that also have an "edge" label and therefore kubernetes will run a gateway pod on these nodes. If you just do an informal deployment and don't need to load balance your service requests, the default setup with only master node acting as a gateway might be sufficient. Otherwise you could balance your service requests on 2-3 edge nodes.

- **edge_count**: number of egde nodes to be created
- **edge_instance_type**: an instance type for the edge nodes (e.g. ``t2.medium``)
- **edge_disk_size**: edges disk size in GB

**Access configuration (optional)**

Typically, you want the end user to access your services through a domain name (e.g. ``servicename.mydomain.com``). One option is to manually configure the DNS services, for a domain name and to load balance the requests among the edge nodes. However, doing this for each deployment can be tedious, and prone to configuration errors. If you just do an informal deployment and don't have a domain name to be used, KubeNow is automatically configuring your setup with the free `nip.io service <http://nip.io/>`_. Otherwise, we recommend to sign up for a free account on `Cloudflare <https://www.cloudflare.com>`_, that you can use as dynamic DNS service for your domain name.

- **use_cloudflare**: set to true if you want KubeNow to configure your cloudflare settings during deployment (if set to false KubeNow will use the free nip.io service instead)
- **cloudflare_email**: the mail that you used to register your Cloudflare account
- **cloudflare_token**: an authentication token that you can generate from the Cloudflare web interface
- **cloudflare_domain**: a zone that you created in your Cloudflare account. This typically matches your domain name (e.g. somedomain.com)

**Network configuration (optional)**

Define the following variables if you want KubeNow to be deployed with an existing VPC and subnet, or security group(s).

- **vpc_id**: ID of an existing VPC (to be defined together with ``subnet_id``)
- **subnet_id**: ID of an existing subnet (to be defined together with ``vpc_id``)
- **additional_sec_group_ids**: list of one or many existing security groups 

Once you are done with your settings you are ready to bootstrap the cluster using Terraform::

  terraform get aws # get required modules (only the first time you deploy)
  terraform apply aws # deploy the cluster

If everything goes well, something like the following message will be printed::


  Apply complete! Resources: X added, 0 changed, 0 destroyed.


To verify that each node connected to the master you can run::

  ansible master -a "kubectl get nodes"

If all of the nodes are not yet connected and in the Ready state, wait a minute and try again. Keep in mind that booting the instances takes a couple of minutes. **Warning** if you are using the free tier, the cluster will take a little bit more to bootstrap (~5 minutes).

Good! Now you have a minimal Kubernetes cluster up and running, and you are ready to :doc:`install the KubeNow core components <install-core>`.
