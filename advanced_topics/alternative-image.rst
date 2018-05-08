Alternative Image
======================
You can use an alternative boot image for your VMs.

Currently KubeNow doesn't support automatic down- and uploading of custom images, therefore you need to make sure image is already present in the cloud provider image repositpoty.
You also need to set the parameter ``skip_image_import = "true"``

Depending on your cloud-provider, change following lines in the ``config.tfvars`` file (Ubuntu Xenial 16.04 is used in the example below).

Amazon::
  
  boot_image = "ubuntu/images/hvm-ssd/ubuntu-xenial-16.04-amd64-server-201803*"
  skip_image_import = "true"
  
Google cloud::

  boot_image = "ubuntu-1604-lts"
  skip_image_import = "true"
  
OpenStack::

  boot_image = "Ubuntu 16.04 LTS (Xenial Xerus) - latest"
  skip_image_import = "true"
  
Microsoft Azure::

  boot_image_public {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }
  skip_image_import = "true"
  
  # On Microsoft Azure you also need to comment out or remove boot_image parameter
  # boot_image = "KubeNow-xx"

  

