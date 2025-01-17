# setup_pemagent

This Ansible Galaxy Role Installs and configure PEM agents.

**Not all Distribution or versions are supported on all the operating systems
available.**

For more details refer to the: *Database engines supported* section.

**Note:**
The role does not configure EDB Postgres Advanced Server or PostgreSQL for
replication it only installs Postgres Enterprise Manager (PEM) agents across
multiple nodes and configure database nodes for PEM monitorning.

**The ansible playbook must be executed under an account that has full
privileges.**

## Requirements

The requirements for this ansible galaxy role are:

  1. Ansible >= 2.9
  2. `community.general`
  3. `edb_devops.edb_postgres` -> `setup_repo` - for installing the EPAS/PG
     repository
  4. `edb_devops.edb_postgres` -> `install_dbserver` - for installing the EPAS/PG
     binaries
  5. `edb_devops.edb_postgres` -> `init_dbserver` - for initializing the EPAS/PG
     data directory and configuring a primary node.

## Role variables

When executing the role via ansible there are three required variables:

  * ***pg_version***

  Postgres Versions supported are: 10, 11, 12, 13 and 14

  * ***pg_type***

  Database Engine supported are: PG and EPAS

The rest of the variables can be configured and are available in the:

  * [roles/setup_pemagent/defaults/main.yml](./defaults/main.yml)

## Dependencies

The `setup_pemagent` role does not have any dependencies on any other roles.

## Example Playbook

### Inventory file content

Content of the `inventory.yml` file:

```yaml
---
all:
  children:
    pemserver:
      hosts:
        pemserver1:
          ansible_host: xxx.xxx.xxx.xxx
          private_ip: xxx.xxx.xxx.xxx
    primary:
      hosts:
        primary1:
          ansible_host: xxx.xxx.xxx.xxx
          private_ip: xxx.xxx.xxx.xxx
          pem_agent: true
          pem_server_private_ip: xxx.xxx.xxx.xxx
    standby:
      hosts:
        standby1:
          ansible_host: xxx.xxx.xxx.xxx
          private_ip: xxx.xxx.xxx.xxx
          upstream_node_private_ip: xxx.xxx.xxx.xxx
          replication_type: synchronous
          pem_agent: true
          pem_server_private_ip: xxx.xxx.xxx.xxx
        standby2:
          ansible_host: xxx.xxx.xxx.xxx
          private_ip: xxx.xxx.xxx.xxx
          upstream_node_private_ip: xxx.xxx.xxx.xxx
          replication_type: asynchronous
          pem_agent: true
          pem_server_private_ip: xxx.xxx.xxx.xxx
```

Note: when setting the `pem_agent_remote` host variable to `true`, PEM agent is
not installed and configured on the database server, but the database server is
registered and monitored by the PEM agent running on the PEM server.

## How to include the `setup_pemagent` role in your Playbook

Below is an example of how to include the `setup_pemagent` role:

```yaml
---
- hosts: primary,standby
  name: Setup PEM agents
  become: yes
  gather_facts: yes

  collections:
    - edb_devops.edb_postgres

  pre_tasks:
    - name: Initialize the user defined variables
      set_fact:
        pg_version: 14
        pg_type: "PG"

  roles:
    - role: setup_pemagent
      # Ensure to execute this role only on the hosts from the primary and
      # standby groups having the 'pem_agent' host var set to true.
      when: "'setup_pemagent' in lookup('edb_devops.edb_postgres.supported_roles', wantlist=True)"
```

Defining and adding variables is done in the `set_fact` of the `pre_tasks`.

All the variables are available at:

  - [roles/setup_pemagent/defaults/main.yml](./defaults/main.yml)
  - [roles/setup_pemagent/vars/EPAS.yml](./vars/EPAS_RedHat.yml)
  - [roles/setup_pemagent/vars/PG.yml](./vars/PG_RedHat.yml)

## Database engines supported

### Community PostgreSQL and PEM

| Distribution                      |               10 |               11 |               12 |               13 |               14 |
| --------------------------------- |:----------------:|:----------------:|:----------------:|:----------------:|:----------------:|
| CentOS 7                          |:white_check_mark:|:white_check_mark:|:white_check_mark:|:white_check_mark:|:white_check_mark:|
| Red Hat Linux 7                   |:white_check_mark:|:white_check_mark:|:white_check_mark:|:white_check_mark:|:white_check_mark:|
| RockyLinux 8                      |:white_check_mark:|:white_check_mark:|:white_check_mark:|:white_check_mark:|:white_check_mark:|
| Red Hat Linux 8                   |:white_check_mark:|:white_check_mark:|:white_check_mark:|:white_check_mark:|:white_check_mark:|
| Ubuntu 20.04 LTS (Focal) - x86_64 |:white_check_mark:|:white_check_mark:|:white_check_mark:|:white_check_mark:|:white_check_mark:|
| Debian 9 (Stretch) - x86_64       |:white_check_mark:|:white_check_mark:|:white_check_mark:|:white_check_mark:|:white_check_mark:|
| Debian 10 (Buster) - x86_64       |:white_check_mark:|:white_check_mark:|:white_check_mark:|:white_check_mark:|:white_check_mark:|

### EnterpriseDB Postgres Advanced Server and PEM

| Distribution                      |               10 |               11 |               12 |               13 |               14 |
| --------------------------------- |:----------------:|:----------------:|:----------------:|:----------------:|:----------------:|
| CentOS 7                          |:white_check_mark:|:white_check_mark:|:white_check_mark:|:white_check_mark:|:white_check_mark:|
| Red Hat Linux 7                   |:white_check_mark:|:white_check_mark:|:white_check_mark:|:white_check_mark:|:white_check_mark:|
| RockyLinux 8                      |               :x:|               :x:|:white_check_mark:|:white_check_mark:|:white_check_mark:|
| Red Hat Linux 8                   |               :x:|               :x:|:white_check_mark:|:white_check_mark:|:white_check_mark:|
| Ubuntu 20.04 LTS (Focal) - x86_64 |               :x:|               :x:|               :x:|:white_check_mark:|:white_check_mark:|
| Debian 9 (Stretch) - x86_64       |               :x:|:white_check_mark:|:white_check_mark:|:white_check_mark:|:white_check_mark:|
| Debian 10 (Buster) - x86_64       |               :x:|               :x:|:white_check_mark:|:white_check_mark:|:white_check_mark:|

- :white_check_mark: - Tested and supported
- :x: - Not supported

## Playbook execution examples

```bash
# To deploy community Postgres version 13 with the user centos
$ ansible-playbook playbook.yml \
  -u centos \
  -i inventory.yml \
  --private-key <key.pem> \
  --extra-vars="pg_version=13 pg_type=PG"
```
```bash
# To deploy EPAS version 12 with the user ec2-user
$ ansible-playbook playbook.yml \
  -u ec2-user \
  -i inventory.yml \
  --private-key <key.pem> \
  --extra-vars="pg_version=12 pg_type=EPAS"
```

## License

BSD

## Author information

Author:

  * Doug Ortiz
  * Vibhor Kumar (Co-Author)
  * EDB Postgres
  * DevOps
  * edb-devops@enterprisedb.com www.enterprisedb.com
