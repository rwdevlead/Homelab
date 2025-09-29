# Docker Compose Services for Homelab

This directory contains Docker Compose configurations for various services running in the homelab environment. Each service is organized in its own directory with its configuration and documentation.

## Services Overview

### Core Infrastructure

- **Traefik** (`traefik/`) - Reverse proxy and SSL termination
  - Handles SSL/TLS termination using Let's Encrypt
  - Provides automatic HTTPS redirection
  - Manages routing to internal services
  - Dashboard available at `proxy.local.rwdevs.com`

### Home Automation

- **Home Assistant** (`homeassistant/`) - Home automation platform

  - Runs on port 8123
  - Integrates with Docker for container management
  - Configured with Traefik labels for reverse proxy

- **Homebridge** (`homebridge/`) - HomeKit bridge for non-native devices
  - Uses host network mode
  - Web interface on port 8581
  - Includes health checks
  - Volume mounted at `CHANGE_TO_COMPOSE_DATA_PATH/homebridge`

### Network Services

- **Pi-hole** (`pi-hole/`) - Network-wide ad blocking
  - Runs on custom macvlan network (192.168.50.41)
  - Configurable via environment variables
  - Persistent storage for configurations
  - Optional Traefik integration (commented out)

### Monitoring & Management

- **Portainer** (managed via Ansible)

  - Container management UI
  - Accessible through Traefik at `portainer.local.rwdevs.com`

- **Watchtower** (`watchtower/`) - Automatic container updates
  - Scheduled updates at midnight (CRON: 0 0 0 \* \* \*)
  - Includes cleanup of old images
  - Optional email notifications (commented out)

### Data & Sync

- **MySQL** (`mySQL/`) - Database server

  - Latest MySQL version
  - Configurable via environment variables
  - Persistent data volume
  - Exposed on port 3306

- **Syncthing** (`syncthing/`) - File synchronization
  - Web UI on port 8384
  - File transfer ports: 22000 (TCP/UDP)
  - Discovery port: 21027 (UDP)
  - PUID/PGID set to 1000

### Dashboard

- **Homepage** (`homepage/`) - Homelab dashboard
  - Accessible on port 3000
  - Traefik integration
  - Custom configurations in `appdata/`
  - Docker socket mounted for container integration

## Network Configuration

The services use several Docker networks:

- `proxy` - External network for Traefik reverse proxy
- `rwdmacvlan` - Macvlan network for services needing direct LAN access (Pi-hole)

## Common Configuration Patterns

1. **Volume Mounting**:

   - Most services use `CHANGE_TO_COMPOSE_DATA_PATH/<service>` for persistent storage
   - Docker socket mounted where container management is needed
   - Local time sync via `/etc/localtime` mount

2. **Networking**:

   - Most web services use the `proxy` network and Traefik labels
   - Services requiring direct network access use `network_mode: host` or custom macvlan

3. **Restart Policies**:

   - Most services use `restart: unless-stopped`
   - Health checks implemented where critical

4. **Security**:
   - Traefik handles SSL/TLS termination
   - Basic auth available for sensitive services
   - Environment variables used for sensitive data

## Usage

### Prerequisites

1. Create required networks:

   ```bash
   docker network create proxy
   ```

2. Set up environment variables:

   - Copy any .env.example files to .env
   - Configure service-specific variables

3. Update data paths:
   - Replace `CHANGE_TO_COMPOSE_DATA_PATH` in compose files with your data directory

### Starting Services

```bash
cd <service-directory>
docker compose up -d
```

### Monitoring

- Access Traefik dashboard: https://proxy.local.rwdevs.com
- View service logs: `docker compose logs -f`
- Check Homepage dashboard: https://homepage.local.rwdevs.com

## Maintenance

### Backup Considerations

- Database volumes (`mysql_data`)
- Configuration volumes (all `CHANGE_TO_COMPOSE_DATA_PATH` directories)
- Traefik SSL certificates (`acme.json`)

### Updates

Watchtower handles automatic updates, but you can manually update:

```bash
docker compose pull
docker compose up -d
```

## Notes

- Review `traefik/config.yml` for routing rules and middleware
- Check service-specific README files for detailed configuration
- Consider using Docker secrets or environment files for sensitive data
- Backup your data and configurations regularly

---

Generated: 2025-09-28
