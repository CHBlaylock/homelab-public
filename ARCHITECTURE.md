# Architecture

## Personal cloud appliance

The primary node is a Raspberry Pi 5 with 2 TB (1.9 TB) NVMe storage.

Core services:

- Immich for photo/video management
- Nextcloud for general file storage
- PostgreSQL for Immich
- MariaDB for Nextcloud
- Redis/Valkey for supporting services
- Tailscale for private remote access

The digital photo frame is physically integrated into the Pi-cloud case through its built-in 4.3-inch touchscreen/display.

## Design principles

- Keep services isolated in containers where practical.
- Keep local access on the LAN.
- Use Tailscale for remote access.
- Automate repetitive synchronization tasks.
- Treat the source application as authoritative when mirroring data.
- Document failures and recovery procedures.
- Verify services after maintenance and reboot.

## Future architecture

A donated Dell OptiPlex 7060 SFF will manage household NAS storage, provide secondary SSD-NAS backup for the Pi-cloud systems, host Jellyfin, and orchestrate weekly backups to a future HDD NAS.
