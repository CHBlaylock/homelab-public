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

## Proxmox virtualization host — deployed

A donated Dell OptiPlex 7060 SFF was inspected and repurposed as a live Proxmox VE 9.2.2 hypervisor. Verified hardware includes an Intel Core i7-8700 (6 cores / 12 threads), 24 GB RAM and 512 GB NVMe storage.

The hypervisor runs an **isolated Ubuntu Server 24.04.3 LTS training VM** on a Linux bridge without a physical uplink or default gateway. Ubuntu networking was configured manually; SSH and systemd health were verified, and a powered-off baseline snapshot was created.

**Remote administration is working:** Tailscale was installed directly on the hypervisor with DNS acceptance disabled. A phone on cellular data successfully accessed the Proxmox management interface through the tailnet; the Tailscale service was verified enabled at boot. No router port forwarding was required.

**Future work, not yet deployed:** additional SATA SSD storage, 2.5 GbE expansion, dedicated four-bay TrueNAS SSD NAS, Jellyfin, off-host backups, weekly HDD-NAS backup tier, and standby DNS infrastructure. Physical inspection found three conventional SATA ports, a removable optical drive, and a 200 W OEM power supply. Drive-power and chassis-fit validation remain pending.

A second independent Pi-cloud appliance is also planned. The selected (not purchased) NAS design uses a Yxk Zero1 Pro with two 2 TB TEAMGROUP Vulcan Z SATA SSDs in a ZFS mirror, a planned internal eMMC TrueNAS boot, and a later expansion path to four 4 TB SSDs. Estimated initial hardware cost is $796.98 before tax and contingencies. See [NAS design](docs/nas-storage.md).

See [Proxmox/Ubuntu deployment](docs/proxmox-ubuntu-lab.md), [remote access](docs/tailscale.md), and [OptiPlex expansion assessment](docs/optiplex-storage-expansion.md).

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

The preferred approach to evaluate is a **cold-standby Pi-hole VM on the existing OptiPlex Proxmox host**, started by health monitoring after a primary outage. Starting a VM alone does not transfer DNS service; safe virtual-IP ownership or another health-checked handoff, split-brain prevention, Tailscale access, configuration synchronization, and failback must be designed and tested. A separate dedicated Pi-hole device remains an alternative and evaluation of health-checked DNS failover, with the goal of keeping LAN and Tailscale clients on filtered DNS during a primary appliance outage. A DNS proxy or virtual IP may be needed if strict primary/standby behavior is required. VM startup adds recovery delay. The hypervisor is now installed; standby DNS remains a separate future project. Planned validation includes simulated outage, continued DNS resolution and filtering, and recovery/failback tests. Client-selected DNS and encrypted DNS bypasses are separate policy considerations.

No private network addresses or device identifiers are disclosed.


## Architecture decisions

[Decision log](docs/decisions.md) records the one-line reason behind each major choice, the alternative considered, and whether it is deployed or planned.

## Mealie recipe manager

Mealie runs in a dedicated Debian VM on Proxmox with Docker Compose. Household LAN and Tailscale remote access are verified. Daily VM and application backups are configured; a manual application archive and off-host SSH/rsync transfer to the Pi 5 NVMe have been verified. The 3:00 AM cron-triggered backup and off-host Pi transfer were verified; an isolated Proxmox VM restore passed boot, service-health, and SQLite integrity checks. A full application-archive restore remains untested. See [Mealie deployment and backup runbook](docs/mealie.md).

**Backup retention update (October 2026):** Pi off-host Mealie archive transfer now includes a manually tested, post-success 30-day age-based cleanup restricted to Mealie archive filenames. Aged-file deletion and application-archive restoration remain untested; the scheduled backup and Proxmox VM restoration have been verified.

## Monitoring — deployed October 13, 2026

Uptime Kuma runs in a dedicated unprivileged Debian 13 Proxmox LXC container (CT 103; 1 vCPU, 512 MiB RAM, 8 GiB disk), managed by systemd and verified to restart automatically after a container reboot. It monitors Mealie HTTP availability and receives push reports from the nightly application-backup workflow. A successful push occurs only after local archive creation and Pi rsync complete; an ERR handler attempts an immediate DOWN push on failure. A simulated DOWN/UP notification test and Gmail SMTP delivery to an iPhone succeeded. The full backup-failure execution path has **not** been deliberately tested. Push heartbeat interval is 26 hours, which is an approximate tolerance rather than a fixed daily deadline. Tailscale cellular access to the dashboard was verified; no public router port forwarding. No private IP addresses, account names, notification tokens or credentials are published.
