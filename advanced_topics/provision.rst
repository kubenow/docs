Provisioning
======================
You can add provision block in your ``config.tfvars``::

The provisioning block is automatically executed via the ``kn apply`` command

You can manyally run only the provisioners with command ``kn provision``

A provision block can have one or several ``action{ }`` blocks

The ``type`` of the ``action{ }`` block can be ``ansible-playbook``, ``helm`` or ``local-exec``

helm::


local-exec::
command



**Action type = "ansible-playbook"**

ansible-playbook speciffic variables::
  
  playbook    = "path-to-playbook (relative config-directory)"
  extra_vars  = "json-object specifying extra varibles (hcl-json-syntax https://github.com/hashicorp/hcl)"

Examples::

  #
  # This block is equivalent to command:
  #
  # ansible-playbook $ANSIBLE_OPT playbooks/install-core.yml
  #
  provision = {
    "action" = {
      "type"       = "ansible-playbook"
      "playbook"   = "playbooks/install-core.yml"
    }
  }
  
  #
  # This block is equivalent to command:
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
  
**Action type = "helm"**

helm speciffic variables::
  
  chart_repo       = "url to chart repo (optional - not needed if chart is in official repo)"
  chart_repo_name  = "a name for the chart repo (optional - not needed if chart is in official repo)"
  chart_name       = "name of chart to apply"
  release_name     = "a release name for this helm application"
  values           = "json-object specifying values (hcl-json-syntax https://github.com/hashicorp/hcl)"
  
  chart_repo
chart_repo_name
chart_name
release_name


Examples::

  #
  # This block is equivalent to command:
  #
  # ansible-playbook $ANSIBLE_OPT playbooks/install-core.yml
  #
  provision = {
    "action" = {
      "type"       = "ansible-playbook"
      "playbook"   = "playbooks/install-core.yml"
    }
  }
  
  #
  # This block is equivalent to command:
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

**Action type = "local-exec"**

local-exec speciffic variables::

  command  = "command to execute - can be path to script (relative config-directory)"
  
Example `local-exec`::

  #
  # This block is equivalent to command:
  #
  # plugins/phnmnl/KubeNow-plugin/bin/phenomenal-post-install.sh
  #
  provision = {
    "action" = {
      "type"       = "local-exec"
      "command"    = "plugins/phnmnl/KubeNow-plugin/bin/phenomenal-post-install.sh"
    }
  }
