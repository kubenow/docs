Alternative Boot Image
======================
KubeNow now allows to specify an alternative boot image of your choice for your cluster VMs. However KubeNow doesn't support yet an automatic downloading and/or uploading for a custom image, therefore you need to make sure such custom (or non-custom image) is already present in the cloud provider image repository you are using for your deployment.

This can be achieved easily by changing just a couple of lines in the ``config.tfvars`` file created by the initial command ``kn init`` as explained `here <../getting_started/bootstrap.html>`_. It is also necessary and important to set the parameter ``skip_image_import`` equal to ``"true"`` in order to avoid triggering the default importing mechanism that KubeNow supports for our customed images.

In the following snippet of code you can see how these changes should look like in the ``config.tfvars`` for each currently supported cloud-provider (a generic Ubuntu Xenial 16.04 image is used in the examples below):  

**Amazon**::
  
  boot_image = "ubuntu/images/hvm-ssd/ubuntu-xenial-16.04-amd64-server-201803*"
  skip_image_import = "true"
  
**Google cloud**::

  boot_image = "ubuntu-1604-lts"
  skip_image_import = "true"
  
**OpenStack**::

  boot_image = "Ubuntu 16.04 LTS (Xenial Xerus) - latest"
  skip_image_import = "true"
  
**Microsoft Azure**::

  boot_image_public {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }
  skip_image_import = "true"
  
  # In this case you also need to comment out or remove the whole boot_image parameter's line
  # boot_image = "KubeNow-xx"

  

