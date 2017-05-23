Image building
==============

KubeNow uses prebuilt images to speed up the deployment. The image building process is now been handled and automated via its related `GitHub repository <https://github.com/kubenow/image>`_ and it mainly relies on using `Packer <http://packer.io/>`_ (0.12.3 or higher).

The KubeNow images are being created and exported on AWS and GCE. In the former builder a related AMI is created across all our main regions (ca-central-1, eu-central-1, eu-west-1, eu-west-2, us-east-1, us-east-2, us-west-1, us-west-2). In the second case, once Packer have built an artifact, a post-processor is being run so to export the image to both our GCE and AWS S3 buckets. Here are the direct URLs to the GCE and AWS pre-built KubeNow images:

- https://storage.googleapis.com/kubenow-images/kubenow-v030a1.tar.gz
- https://s3.amazonaws.com/kubenow-us-east-1/kubenow-current.qcow2

This way a Kubenow cluster can easily and quickly be spawn on your preferred provider.

Versioning
~~~~~~~~~~
It is important to point out that the image versioning is now different from the KubeNow versioning. The main reason lies in the fact that pre-built image will require less revisions and updates compared to the main KubeNow package. Hence you can expect KubeNow versioning being often higher than the image one.