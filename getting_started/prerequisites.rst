Prerequisites
=============

Install Docker
--------------

KubeNow provisioners are distributed via `Docker <https://www.docker.com/>`_. Please start by installing Docker on your workstation: `Install Docker <https://docs.docker.com/engine/installation/>`_.

Get KubeNow
-----------

In order to get the provisioning tools please run:

.. parsed-literal::

  docker pull kubenow/provisioners:|release|

We wrote up a handy CLI that wraps around the Docker container above, you can install it with a one-liner:

.. ifconfig:: release == "master"

  .. parsed-literal::

    curl -Lo kn \https://raw.githubusercontent.com/kubenow/KubeNow/|release|/bin/kn && chmod +x kn && sudo mv kn /usr/local/bin/

.. ifconfig:: release != "master"

  .. parsed-literal::

    curl -Lo kn https://github.com/kubenow/KubeNow/releases/download/|release|/kn && chmod +x kn && sudo mv kn /usr/local/bin/
