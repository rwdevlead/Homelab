# Ansible playbooks for Homelab

This folder contains a set of Ansible playbooks, roles and an inventory used to automate common tasks across the Homelab hosts (Docker host, MySQL host, and other VMs).

This README documents the layout, what each playbook does, required dependencies and examples for running the playbooks.

## Quick layout

ansible/

- inventory/ # Inventory files (hosts/groups)
- playbooks/ # Playbooks and role directories
  - inst-net-tools.yml # Install networking tools (net-tools)
  - inst-qemo-guest-agent.yml # Install qemu-guest-agent on VMs
  - update-apt.yml # Perform apt dist-upgrade, autoremove and optional reboot
  - Docker-Portainer/ # Playbooks & roles for Docker + Portainer deployment
    - inst-docker-portainer.yml
    - maint-docker-clean.yml
    - roles/
      - docker_install/ # Role to install Docker CE and configure daemon
      - portainer_deploy/ # Role to deploy Portainer using docker-compose
  - mySQL-docker/ # Deploy MySQL as a Docker service
  - mySQL-metal/ # Install MySQL on a VM (native package)

## Inventory

The repository includes a simple static inventory at `inventory/inventory`. It groups hosts by purpose:

Example:

```ini
[docker]
192.168.50.14

[mySQL]
192.168.50.13
```

Adjust this file (or use your own inventory) to match your environment. You can also use an Ansible inventory plugin, dynamic inventory, or pass `-i` with a different file.

## Playbooks overview

- `inst-net-tools.yml` — Ensures the `net-tools` package is installed on targeted hosts.
- `inst-qemo-guest-agent.yml` — Ensures `qemu-guest-agent` is installed on VMs (useful for cloud/VM tooling).
- `update-apt.yml` — Runs `apt dist-upgrade`, checks for a reboot-required file and performs `autoremove`.

Docker / Portainer

- `playbooks/Docker-Portainer/inst-docker-portainer.yml` — Installs Docker CE and deploys Portainer by running two roles:

  - `docker_install` — Installs Docker dependencies, registers Docker repository and installs `docker-ce`; renders `/etc/docker/daemon.json` from a template and restarts Docker.
  - `portainer_deploy` — Ensures `docker-compose` is present, creates a folder for Portainer compose files, writes a `docker-compose.yml` from a role template and runs the compose using the `community.docker.docker_compose` module.

- `playbooks/Docker-Portainer/maint-docker-clean.yml` — Uses `community.docker.docker_prune` to remove non-dangling images and free space.

MySQL

- `playbooks/mySQL-docker/inst-mySQL.yml` — Deploys MySQL inside Docker on hosts in the `docker` group using a role that writes a compose file and brings it up.
- `playbooks/mySQL-metal/inst-mySQL.yml` — Installs native MySQL packages on hosts in the `mySQL` group and configures users/databases.

## Roles and templates

- Docker role variables:
  - `docker_apt_release_channel`, `docker_apt_repository`, `docker_daemon_options` are provided in `roles/docker_install/vars/main.yml`.
- Portainer role variables:
  - `portainer_version`, `docker_compose_folder` defined in `roles/portainer_deploy/vars/main.yml`.
- The roles include templates for `docker_daemon.json.j2` and `docker_compose.yml.j2` used for Portainer and MySQL deployments.

## Requirements

- Ansible (2.9+ recommended). Some playbooks use `community.docker` and `community.mysql` collections.
- On the control node, install the collections if you intend to run the Docker-related playbooks:

```bash
ansible-galaxy collection install community.docker community.mysql
```

- The Docker-related playbooks assume the remote host is Ubuntu (apt-based) and that you can escalate to root (become: true). Modify roles if using a different distro.

## Variables and customization

- Most roles include `vars/main.yml` — review them and override with group_vars/host_vars or `--extra-vars` as needed (for example, database passwords, `docker_compose_folder`, or `portainer_version`).
- Example variables used by the mySQL role template: `mysql_version`, `mysql_root_password`, `mysql_database_name`, `mysql_user_name`, `mysql_user_password` — ensure these are provided when running the playbook.

## Running playbooks

Example commands (from repository root):

```bash
# Run a simple playbook against inventory
ansible-playbook -i ansible/inventory/inventory ansible/playbooks/inst-net-tools.yml

# Install Docker + Portainer on the Docker host
ansible-playbook -i ansible/inventory/inventory ansible/playbooks/Docker-Portainer/inst-docker-portainer.yml

# Deploy MySQL in Docker (ensure necessary vars are provided)
ansible-playbook -i ansible/inventory/inventory \
  -e "mysql_version=8.0 mysql_root_password=secret mysql_database_name=appdb mysql_user_name=appuser mysql_user_password=apppass" \
  ansible/playbooks/mySQL-docker/inst-mySQL.yml
```

## Notes & next steps

- Consider moving secrets (passwords) into Ansible Vault or an external secrets manager instead of passing them on the command line or storing them in plain text.
- You can add `group_vars/` and `host_vars/` directories under `ansible/` for persistent configuration per host/group.
- The Docker roles use `docker-compose` and `community.docker.docker_compose` — ensure the remote host has the required Python Docker bindings or run via the system docker-compose binary depending on your setup.

If you want, I can also:

- Add example `group_vars/` files for `docker` and `mySQL` groups.
- Convert the inventory to an `ansible.cfg`-aware layout and add a sample `ansible.cfg`.
- Add Vault examples to store MySQL credentials securely.

---

Generated on: 2025-09-28
