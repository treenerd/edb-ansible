# remove_cloudnativepg_sandbox

This Ansible Galaxy Role Removes CloudNativePG Sandbox.

**The ansible playbook must be executed under an account that has full
privileges.**

## Requirements

The only dependencies required for this ansible galaxy role are:

  1. Ansible
  2. Python packages: openshift, pyyaml, kubernetes, pyhelm
     Installed with commands:
     `pip install openshift pyyaml kubernetes`
     `pip install pyhelm`
  3. `kubernetes.core.helm_repository` - Ansible Module - Required for Helm.
  4. `community.kubernetes.helm` - Ansible Module - Required for Helm.
  5. `kubernetes.core.k8s` - Ansible Module - Required for Kubernetes.

## Role variables

There are no required variables:

## Dependencies

The `remove_cloudnativepg_sandbox` role does not have any dependencies on any other roles.

## Example Playbook

## How to include the `remove_cloudnativepg_sandbox` role in your Playbook

Below is an example of how to include the `remove_cloudnativepg_sandbox` role:

```yaml
---
- hosts: localhost
  name: Deploy CloudNativePG-Sandbox Playbook
  gather_facts: yes

  roles:
    - remove_cloudnativepg_sandbox
```

## Playbook execution examples

```bash
# To Remove CloudNativePG Sandbox from Kubernetes Cluster
$ ansible-playbook playbook.yml 
```

## License

BSD

## Author information

Author:

  * Doug Ortiz
  * Julien Tachoires
  * Vibhor Kumar
  * DevOps
  * edb-devops@enterprisedb www.enterprisedb.com