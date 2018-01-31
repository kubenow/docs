Cloudflare: Proxied Traffic
===========================
In addition to the dynamic DNS service, the traffic can be proxied through the `Cloudflare <https://www.cloudflare.com>`_ servers, using a fully encrypted connection. When operating in this mode Cloudflare provides HTTPS connections to the end user, and it protects against distributed denial of service, customer data breach and malicious bot abuse.

Cloudflare's proxy settings can be changed within the `terraform.tfvars` created automatically when initializing a :doc:`deploy configuration directory <../getting_started/bootstrap>` by running::

  kn init <your-provider> my_deployment

Precisely, here below can be found an example of the configuration lines to be optionally tweaked in case a user had a different, dedicated Cloudflare account::

  # Cloudflare proxy (optional)
  # cloudflare_proxied = "true"
  # cloudflare_record_texts = ["record1","record2",...] # Warn: wildcards are not supported when using proxied records

Like the other Cloudflare's settings, it is enough to decomment these lines and modify the above placeholders with others Cloudflare settings. Again, since for entry-level users it can be difficult to reserve a domain name and set it up with Cloudflare, it is possible to not use proxied traffic.