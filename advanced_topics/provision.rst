Provisioning
============
It is now possible to both automatically and manually provision the cluster's instances via the master node. This is done by simply adding a provision block in your ``config.tfvars`` (which by now you should be familiar with). If available, such block will be automatically executed when the command ``kn apply`` is run. Otherwise we can also manually execute it with the new command ``kn provision``.

A provision block can have one or several ``action{ }`` sub-block/s. The latter will always have at least a ``type`` field, which can either be ``ansible-playbook`` or ``local-exec``. Based on these two scenarios, it is then necessary to introduce a couple of specific variables as shown in the examples here below.

**Note:** The Ansible version being used is the one installed in the KubeNow Docker image.

Action type = "ansible-playbook"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In this scenario, the following *ansible-playbook* specific variables will be used, with the exception of ``extra_vars`` which is optional::
  
  playbook    = "path-to-playbook (relative config-directory)"
  extra_vars  = "json-obj of extra variables (complying to hcl-json-syntax, see https://github.com/hashicorp/hcl)"

Example **without** ``extra_vars``::

  #
  # This block is equivalent to the command:
  #
  # ansible-playbook $ANSIBLE_OPT playbooks/install-core.yml
  #
  provision = {
    "action" = {
      "type"       = "ansible-playbook"
      "playbook"   = "playbooks/install-core.yml"
    }
  }
  
Example **with** ``extra_vars``::

  #
  # This block is equivalent to the command:
  #
  # ansible-playbook $ANSIBLE_OPT -e "$extra_vars" playbooks/create-pvc.yml
  #
  provision = {
    "action" = {
      "type"      = "ansible-playbook"
      "playbook"  = "playbooks/create-pvc.yml"
      "extra_vars" = {
        "claim_name"  = "galaxy-pvc"
        "storage"     = "95G"
      }
    }
  }

Action type = "local-exec"
~~~~~~~~~~~~~~~~~~~~~~~~~~~

In this other case, we only have one *local-exec* specific variable that must be present in order to work::

  command  = "command to execute - can be path to a script (relative config-directory)"
  
Example::

  #
  # This block is equivalent to the command:
  #
  # plugins/phnmnl/KubeNow-plugin/bin/phenomenal-post-install.sh
  #
  provision = {
    "action" = {
      "type"       = "local-exec"
      "command"    = "plugins/phnmnl/KubeNow-plugin/bin/phenomenal-post-install.sh"
    }
  }
