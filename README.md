# Ansible OpenStack Prerequisites

Ansible roles that install and configure the base services required by an OpenStack environment, following the official OpenStack install guide.

## What this covers
- One role per service: `ntp` (chrony), `SQL` (MariaDB), `message` (RabbitMQ), `memcached`, `ETCD`, and `openstack` (client packages)
- Writing service configuration files with the `copy` module
- Creating a RabbitMQ user with the `community.rabbitmq` collection
- Running updates in `pre_tasks` before the role plays

## Lab environment
- Control node: Ubuntu workstation running Ansible
- Managed node: one Ubuntu server (VirtualBox, host-only network)

## Repository structure
```
.
├── ansible.cfg
├── inventory
├── Openstack.yml        # pre_tasks + role plays
└── roles/
    ├── ETCD/tasks/main.yml
    ├── SQL/tasks/main.yml
    ├── memcached/tasks/main.yml
    ├── message/tasks/main.yml
    ├── ntp/tasks/main.yml
    └── openstack/tasks/main.yml
```

## Usage
```bash
ansible-collection install community.rabbitmq   # if not already installed
ansible-playbook --ask-become-pass Openstack.yml
```

## Verification
The playbook completed with no failures (`ok=26 changed=18 failed=0`). On the managed node:

| Service | Result |
|---------|--------|
| MariaDB | active (running), version 10.6.12 |
| RabbitMQ | active (running) |
| Memcached | active (running) |

## Notes and next steps
- Configuration values are placeholders from the install guide (`NTP_SERVER`, `RABBIT_PASS`). Replace them with real values, and move secrets into Ansible Vault.
- The service tasks enable services but do not restart them after config changes, so new settings may not take effect until a restart. Handlers would fix this.
- Everything targets a single `Ubuntu` host group. Separate `controller` and `compute` groups, as in the install guide, are the next step.
- Chrony and etcd were not checked in this lab.
