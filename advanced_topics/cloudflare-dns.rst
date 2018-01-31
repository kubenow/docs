Cloudflare DNS Records
======================
`Cloudflare <https://www.cloudflare.com>`_ runs one of the largest authoritative DNS networks in the world. With an average of a few milliseconds query speed, DNS updates happen within seconds.

Thus in order to resolve domain names for the Kubenow exposed services, the deployment script adds a wildcard record to the Cloudflare dynamic DNS service, such that a configurable base domain name will resolve to either the edge nodes or the master node when enabled to be working as an edge node.

Cloudflare's settings can be changed within the `terraform.tfvars` created automatically when initializing a :doc:`deploy configuration directory <../getting_started/bootstrap>` by running::

  kn init <your-provider> my_deployment

Precisely, here below can be found an example of the configuration lines to be optionally tweaked in case a user had a different, dedicated Cloudflare account::

  # Cloudflare configuration (optional)
  # use_cloudflare = "true"
  # cloudflare_email = "your-cloudflare-email"
  # cloudflare_token = "your-cloudflare-token"
  # cloudflare_domain = "your-domain-name"
  # cloudflare_subdomain = "your-subdomain-name"

Thus it suffices to just decomment these lines and modify the above placeholders with others Cloudflare settings. Nevertheless, since for entry-level users it can be difficult to reserve a domain name and set it up with Cloudflare, it is possible to use NIP.IO instead.

NIP.IO provides for an easy mechanism to resolve domain names without needing any configuration (e.g., foo.10.0.0.1.nip.io maps to 10.0.0.1, bar.10.0.0.2.nip.io maps to 10.0.0.2, etc.).