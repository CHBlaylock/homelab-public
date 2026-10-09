# Self-Hosted Homelab & Personal Cloud

A hands-on infrastructure project demonstrating Linux administration, Docker, networking, storage, automation, troubleshooting, and technical documentation.

This is the public portfolio version of a private homelab. Real network addresses and other private infrastructure details are intentionally omitted.

## Current project

A Raspberry Pi 5 personal-cloud appliance uses a 2 TB (1.9 TB) NVMe SSD.

Services include:

- Immich — self-hosted photo and video library
- Nextcloud — general-purpose file cloud
- PostgreSQL — Immich database
- MariaDB — Nextcloud database
- Redis/Valkey — supporting application services
- Tailscale — private remote access
- systemd — scheduled synchronization and service management

The Pi also has an integrated digital photo frame using the case's built-in 4.3-inch touchscreen/display.

## Digital photo frame

The frame combines labwc, swayimg, Bash, Python, the Immich API, a systemd timer, and a local synchronized photo directory.

The Immich album is the authoritative photo set. Added photos are downloaded to the frame and removed album photos are removed only from the frame's local copy.

A slideshow manager watches for directory changes, refreshes swayimg when necessary, and restores the slideshow after reboot.

## Network design

Local access uses the LAN. Remote access uses Tailscale.

Tailscale provides private remote connectivity without public port forwarding. Actual addresses and tailnet identifiers are omitted from this public repository.

## Planned infrastructure

A donated Dell OptiPlex 7060 SFF (reported Intel Core i7-8700, 24 GB RAM, 512 GB drive of unverified type) is planned as the management server. The computer has not yet been powered on or inspected. Proxmox VE is the recommended future host operating system, not installed. Planned roles include:

1. Household NAS
2. Secondary SSD-NAS backup for the personal-cloud systems
3. Jellyfin media server
4. Weekly backup orchestration to a future HDD NAS
5. Backup Pi-hole VM and DNS health monitoring

A second independent Pi-cloud appliance is also planned using the same general architecture.

## Skills demonstrated

- Linux / Debian / Raspberry Pi OS
- Windows troubleshooting
- Docker and containerized applications
- PostgreSQL / MariaDB
- Redis / Valkey
- LAN networking and routing
- Tailscale VPN
- DNS and trusted-domain configuration
- Bash scripting
- Python automation
- REST/API integration
- systemd services and timers
- NVMe storage
- NAS and backup architecture
- Process and service troubleshooting
- Git/GitHub
- Technical documentation

## Documentation

- Architecture
- Networking
- Docker
- Security
- Pi-cloud
- Digital photo frame
- Pi-hole
- Tailscale
- Issues and fixes
- Maintenance
- [Equipment inventory — exact Raspberry Pi 3 B+ and Raspberry Pi 5 build components](docs/equipment.md)
- [Pi-hole appliance](docs/pi-hole.md)

This repository is intentionally sanitized for public viewing while preserving the technical architecture, troubleshooting lessons, and skills demonstrated by the project.

## Future DNS resilience

**Planned, not implemented.** The network currently uses a dedicated wired Raspberry Pi Pi-hole appliance for DNS filtering, with a public DNS fallback available to clients. A secondary DHCP DNS entry is not strict failover: clients may use either resolver while both are available.

The preferred approach to evaluate is a **cold-standby Pi-hole VM on the existing OptiPlex Proxmox host**, started by health monitoring after a primary outage. Starting a VM alone does not transfer DNS service; safe virtual-IP ownership or another health-checked handoff, split-brain prevention, Tailscale access, configuration synchronization, and failback must be designed and tested. A separate dedicated Pi-hole device remains an alternative and evaluation of health-checked DNS failover, with the goal of keeping LAN and Tailscale clients on filtered DNS during a primary appliance outage. A DNS proxy or virtual IP may be needed if strict primary/standby behavior is required. VM startup adds recovery delay. Initial work is to boot and verify the donated hardware and existing Windows drive before any potentially destructive Proxmox installation. Planned validation includes simulated outage, continued DNS resolution and filtering, and recovery/failback tests. Client-selected DNS and encrypted DNS bypasses are separate policy considerations.

No private network addresses or device identifiers are disclosed.

