Cloudflare: Proxied Traffic
===========================
Incoming container traffic can be optionally proxied through the `Cloudflare <https://www.cloudflare.com>`_ servers. When operating in this mode Cloudflare provides HTTPS for container services, and it protects against distributed denial of service, customer data breach and malicious bot abuse.

To enable Cloudflare proxied traffic, it is sufficient to uncomment the following lines in the ``terraform.tfvars`` file, specifying DNS records to be proxied::

  # Cloudflare proxy (optional)
  cloudflare_proxied = "true"
  cloudflare_record_texts = ["record1","record2",...] # Warn: wildcards are not supported when using proxied records
