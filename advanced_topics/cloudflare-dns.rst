Cloudflare DNS Records
======================
`Cloudflare <https://www.cloudflare.com>`_ runs one of the largest authoritative DNS networks in the world. In order to resolve domain names for exposed services, KubeNow can optionally configure the Cloudflare dynamic DNS service, so that a base domain name will resolve to the edge nodes (or the master node if no edge node is deployed).

To configure the Cloudflare dynamic DNS service, it is sufficient to uncomment the following lines in the ``config.tfvars`` file, specifying credentials, domain and subdomain::

  # Cloudflare configuration (optional)
  use_cloudflare = "true"
  cloudflare_email = "your-cloudflare-email"
  cloudflare_token = "your-cloudflare-token"
  cloudflare_domain = "your-domain-name"
  cloudflare_subdomain = "your-subdomain-name"
