Deploy KubeNow on a host cloud
==============================
The following steps are slightly different for each host cloud. Here you find a section for each of the supported providers.

.. contents:: Sections
  :depth: 2

Deploy on OpenStack
-------------------

Prerequisites
~~~~~~~~~~~~~

In this section we assume that:

- You have downloaded and sourced the OpenStack RC file for your tenancy: ``source project-openrc.sh`` (https://docs.openstack.org/user-guide/common/cli-set-environment-variables-using-openstack-rc.html#download-and-source-the-openstack-rc-file)
- You have a floating IP quota that allows to allocate at least one public IP

Deployment configuration
~~~~~~~~~~~~~~~~~~~~~~~~
First we need to initialize a deploy configuration directory by running::

  kn init my_deployment

The configuration directory contains a new SSH key pair for your deployments, and some `Terraform <http://terraform.io/>`_ configuration templates that we need to fill in.

Locate into ``my_deployment`` and create a ``terraform.tfvars`` configuration file, copying the OpenStack template::

  cd my_deployment
  cp terraform.tfvars.os-template terraform.tfvars

In this configuration file you will need to set at least:

**Cluster configuration**

- **cluster_prefix**: every resource in your tenancy will be named with this prefix
- **floating_ip_pool**: a floating IP pool label
- **external_network_uuid**: the uuid of the external network in the OpenStack tenancy

If you are wondering where you can get ``floating_ip_pool`` and ``external_network_uuid``, then one way is to inquiry your OpenStack settings by running::

  kn openstack network list

Depending on your tenancy settings you should get a similar output::

  +--------------------------------------+----------------+...
  | ID                                   | Label          |...
  +--------------------------------------+----------------+...
  | 5f274562-89b6-4ab2-a18f-94b159b0b85d | internal       |...
  | d9384930-baa5-422b-8657-1d42fb54f89c | net_external   |...
  +--------------------------------------+----------------+...

Thus in this specific case the above mentioned fields will be set as it follows::

  floating_ip_pool = "net_external"
  external_network_uuid = "d9384930-baa5-422b-8657-1d42fb54f89c"


**Master configuration**

  - **master_flavor**: an instance flavor for the master

**Node configuration**

  - **node_count**: number of Kubernetes nodes to be created (no floating IP is needed for these nodes)
  - **node_flavor**: an instance flavor name for the Kubernetes nodes

If you are wondering yet again where you can fetch correct flavor label names then no worries, you are not being a stranger here. The openstack command-line interface will come in handy. Just run the following command::

  kn openstack flavor list

Depending on your tenancy settings you should get a similar output::

    +--------+------------+...
    | ID     | Name       |...
    +--------+------------+...
    | 8c7ef1 | ssc.tiny   |...
    | 8d7ef2 | ssc.small  |...
    | 8e7ef3 | ssc.medium |...
    | 8f7ef4 | ssc.large  |...
    | 8g7ef5 | ssc.xlarge |...
    +--------+------------+...

You may want to select the favor according to much resources you'd like to allocate. E.g.::

    master_flavor = "ssc.medium"
    node_flavor = "ssc.large"

Deploy KubeNow
~~~~~~~~~~~~~~
Once you are done with your settings you are ready deploy your cluster running::

  kn apply openstack

The first time you are going to deploy it will take longer, since the KubeNow image needs to be imported. Future deployments will be considerably faster, since the image will be already present in your user space.

To check that your cluster is up and running you can run::

  kn kubectl get nodes

As long as you are in the ``my_deployment`` directory you can use ``kubectl`` over SSH to control Kubernetes. If everything went well, now you are ready to :doc:`deploy your first application <first-app>`.

Deploy on Google Cloud (GCE)
----------------------------

Prerequisites
~~~~~~~~~~~~~

In this section we assume that:

- You have enabled the Google Compute Engine API: API Manager > Library > Compute Engine API > Enable
- You have created and downloaded a service account file for your GCE project: Api manager > Credentials > Create credentials > Service account key

Deployment configuration
~~~~~~~~~~~~~~~~~~~~~~~~
First we need to initialize a deploy configuration directory by running::

  kn init my_deployment

The configuration directory contains a new SSH key pair for your deployments, and some `Terraform <http://terraform.io/>`_ configuration templates that we need to fill in.

Locate into into ``my_deployment`` and create a ``terraform.tfvars`` configuration file, copying the GCE template::

  cd my_deployment
  cp terraform.tfvars.gce-template terraform.tfvars

In this configuration file you will need to set at least:

**Cluster configuration**

- **cluster_prefix**: every resource in your project will be named with this prefix (the name must match ``(?:[a-z](?:[-a-z0-9]{0,61}[a-z0-9])?)``, e.g. "kubenow")

**Google credentials**

- **gce_project**: your project id
- **gce_zone**: some GCE zone (e.g. ``europe-west1-b``)

**Master configuration**

- **master_flavor**: an instance flavor for the master (e.g. ``n1-standard-2``)
- **master_disk_size**: master disk size in GB

**Node configuration**

- **node_count**: number of Kubernetes nodes to be created
- **node_flavor**: an instance flavor for the Kubernetes nodes (e.g. ``n1-standard-2``)
- **node_disk_size**: nodes disk size in GB

In addition, when deploying on GCE you need to copy your service account file in the deployment configuration directory::

  # assuming that you are in my_deployment
  cp /path/to/service-account.json ./

Deploy KubeNow
~~~~~~~~~~~~~~
Once you are done with your settings you are ready deploy your cluster running::

  kn apply gce

The first time you are going to deploy it will take longer, since the KubeNow image needs to be imported. Future deployments will be considerably faster, since the image will be already present in your user space.

To check that your cluster is up and running you can run::

  kn kubectl get nodes

As long as you are in the ``my_deployment`` directory you can use ``kubectl`` over SSH to control Kubernetes. If everything went well, now you are ready to :doc:`deploy your first application <first-app>`.

Deploy on Amazon Web Services (AWS)
-----------------------------------

Prerequisites
~~~~~~~~~~~~~

In this section we assume that:

- You have an IAM user along with its ``access key`` and ``security credentials`` (http://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html)

Deployment configuration
~~~~~~~~~~~~~~~~~~~~~~~~
First we need to initialize a deploy configuration directory by running::

  kn init my_deployment

The configuration directory contains a new SSH key pair for your deployments, and some `Terraform <http://terraform.io/>`_ configuration templates that we need to fill in.

Locate into ``my_deployment`` and create a ``terraform.tfvars`` configuration file, copying the AWS template::

  cd my_deployment
  cp terraform.tfvars.aws-template terraform.tfvars

In this configuration file you will need to set at least:

**Cluster configuration**

- **cluster_prefix**: every resource in your tenancy will be named with this prefix
- **aws_region**: the region where your cluster will be bootstrapped (e.g. ``eu-west-1``)
- **availability_zone**: an availability zone for your cluster (e.g. ``eu-west-1a``)


**Credentials**

- **aws_access_key_id**: your access key id
- **aws_secret_access_key**: your secret access key

**Master configuration**

- **master_instance_type**: an instance type for the master (e.g. ``t2.medium``)
- **master_disk_size**: edges disk size in GB

**Node configuration**

- **node_count**: number of Kubernetes nodes to be created
- **node_instance_type**: an instance type for the Kubernetes nodes (e.g. ``t2.medium``)
- **node_disk_size**: edges disk size in GB

Deploy KubeNow
~~~~~~~~~~~~~~
Once you are done with your settings you are ready deploy your cluster running::

  kn apply aws

To check that your cluster is up and running you can run::

  kn kubectl get nodes

As long as you are in the ``my_deployment`` directory you can use ``kubectl`` over SSH to control Kubernetes. If everything went well, now you are ready to :doc:`deploy your first application <first-app>`.

Deploy on Microsoft Azure
----------------------------

Prerequisites
~~~~~~~~~~~~~

In this section we assume that:

- You have created an application API key (Service Principal) in your Microsoft Azure subscription: (https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html#creating-a-service-principal)

Deployment configuration
~~~~~~~~~~~~~~~~~~~~~~~~
First we need to initialize a deploy configuration directory by running::

  kn init my_deployment

The configuration directory contains a new SSH key pair for your deployments, and some `Terraform <http://terraform.io/>`_ configuration templates that we need to fill in.

Locate into into ``my_deployment`` and create a ``terraform.tfvars`` configuration file, copying the Azure template::

  cd my_deployment
  cp terraform.tfvars.azure-template terraform.tfvars

In this configuration file you will need to set at least:

**Cluster configuration**

- **cluster_prefix**: every resource in your tenancy will be named with this prefix
- **location**: some Azure location (e.g. ``West Europe``)

**Azure credentials**

- **subscription_id**: your subscription id
- **client_id**: your client id (also called appId)
- **client_secret**: your client secret (also called password)
- **tenant_id**: your tenant id

**Master configuration**

- **master_flavor**: an instance flavor for the master (e.g. ``Standard_DS2_v2``)
- **master_disk_size**: master disk size in GB

**Node configuration**

- **node_count**: number of Kubernetes nodes to be created
- **node_flavor**: an instance flavor for the Kubernetes nodes (e.g. ``Standard_DS2_v2``)
- **node_disk_size**: nodes disk size in GB

Deploy KubeNow
~~~~~~~~~~~~~~
Once you are done with your settings you are ready deploy your cluster running::

  kn apply azure

The first time you are going to deploy it will take longer, since the KubeNow image needs to be imported. Future deployments will be considerably faster, since the image will be already present in your user space.

To check that your cluster is up and running you can run::

  kn kubectl get nodes

As long as you are in the ``my_deployment`` directory you can use ``kubectl`` over SSH to control Kubernetes. If everything went well, now you are ready to :doc:`deploy your first application <first-app>`.
