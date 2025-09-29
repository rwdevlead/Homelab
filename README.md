# Homelab Infrastructure as Code

This repository contains the complete Infrastructure as Code (IaC) and configuration management for my homelab environment. It includes everything needed to set up, configure, and maintain the infrastructure using various tools like Ansible, Docker Compose, and Packer.

## Repository Structure

```
Homelab/
├── ansible/                    # Ansible playbooks and roles
│   ├── inventory/             # Host inventory definitions
│   └── playbooks/             # Organized playbooks for different services
│       ├── Docker-Portainer/  # Docker and Portainer installation/config
│       ├── mySQL-docker/      # MySQL deployment in Docker
│       └── mySQL-metal/       # Bare metal MySQL installation
├── docker-compose/            # Docker Compose service definitions
│   ├── homeassistant/        # Home automation platform
│   ├── homebridge/           # HomeKit integration
│   ├── homepage/             # Dashboard for services
│   ├── mySQL/                # Database service
│   ├── pi-hole/             # Network-wide ad blocking
│   ├── syncthing/           # File synchronization
│   ├── traefik/             # Reverse proxy and SSL termination
│   └── watchtower/          # Automatic container updates
├── dotfiles/                 # Personal configuration files
└── packer/                   # VM image definitions
    ├── ubuntu-server-focal/           # Ubuntu 20.04 base image
    └── ubuntu-server-focal-docker/    # Ubuntu 20.04 with Docker
```

## Core Components

### 1. Infrastructure Provisioning (Packer)

- Base VM images for Ubuntu Server 20.04
- Specialized images with Docker pre-installed
- Custom configurations for Proxmox deployment

### 2. Configuration Management (Ansible)

- Automated server setup and configuration
- Docker and container management
- Database installation and configuration
- Network tools and system updates

### 3. Container Orchestration (Docker Compose)

- Reverse proxy with automatic SSL (Traefik)
- Home automation stack (Home Assistant, Homebridge)
- Network services (Pi-hole)
- Monitoring and management (Portainer, Watchtower)
- Data services (MySQL, Syncthing)

## Quick Start

### Prerequisites

- Proxmox VE for VM hosting
- Ansible 2.9+ on control node
- Docker and Docker Compose on container hosts
- Git for repository management

### Initial Setup

1. Clone the repository:

```bash
git clone https://github.com/rwdevlead/Homelab.git
cd Homelab
```

2. Build VM images (requires Packer and Proxmox):

```bash
cd packer/ubuntu-server-focal
packer build ubuntu-server-focal.pkr.hcl
```

3. Configure Ansible inventory:

```bash
cd ../../ansible
cp inventory/inventory.example inventory/inventory
# Edit inventory/inventory with your host details
```

4. Deploy base configuration:

```bash
ansible-playbook -i inventory/inventory playbooks/update-apt.yml
ansible-playbook -i inventory/inventory playbooks/inst-net-tools.yml
```

5. Deploy services:

```bash
# Install Docker + Portainer
ansible-playbook -i inventory/inventory playbooks/Docker-Portainer/inst-docker-portainer.yml

# Configure Docker services
cd ../docker-compose
# Follow README.md in each service directory
```

## Network Architecture

- Traefik handles reverse proxy and SSL termination
- Internal services accessible via `*.local.rwdevs.com`
- Dedicated Docker networks for service isolation
- Macvlan networking for services requiring direct LAN access

## Maintenance

### Backups

- VM backups handled by Proxmox
- Container volumes backed up via host system
- Database dumps for MySQL instances
- Configuration files version controlled in this repo

### Updates

- System updates via Ansible playbooks
- Container updates managed by Watchtower
- Manual updates documented in service directories

## Security Considerations

- SSL/TLS termination via Traefik
- Secrets management using environment files
- Network segregation using Docker networks
- Regular security updates via automation

## Contributing

1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Create a Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- Home Assistant community for automation examples
- Traefik team for excellent reverse proxy
- Various open-source projects used in this homelab

---

Generated: 2025-09-28
Last tested with:

- Ansible 2.9+
- Docker 24.0+
- Ubuntu Server 20.04 LTS
- Proxmox VE 7+
