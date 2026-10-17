# Media server deployment (VM 104)

Status: **VM provisioned; application containers not yet deployed**, verified 2026-10-16.

## Objective

Provide household movies, television, music, books and audiobooks through self-hosted services. Use existing Tailscale infrastructure for remote access and a **separate** VPN with a fail-closed network path for torrent traffic. Do not start torrent downloads until that isolation is verified.

## Verified VM state

- Proxmox guest: VM 104, Ubuntu-based `media-server` (OS release/version not independently checked in this handoff).
- Docker Engine 29.9.1 and Docker Compose v5.6.0 installed.
- `docker ps -a` returned **no containers** at the time of inspection.
- 64 GiB virtual disk, LVM-backed root filesystem; approximately 50 GiB available.
- LAN connectivity confirmed; exact addresses omitted.
- No separate media disk/mount appeared in the supplied `lsblk` and `df -h` output. Existing library directories currently reside on the VM filesystem unless later storage is mounted.

## Prepared filesystem

- `/opt/media-stack/config/`: dedicated configuration directories for Jellyfin, Jellyseerr, qBittorrent, Sonarr, Radarr, Prowlarr, Lidarr, Audiobookshelf, and Calibre-Web Automated.
- `/srv/media/library/`: movies, tv, music, audiobooks, ebooks, podcasts.
- `/srv/media/downloads/`: incomplete and complete.
- `/srv/media/quarantine/` and `/srv/media/validation/`: previously prepared holding/inspection directories; no custom validation pipeline is required to deploy Jellyfin.

The directories have differentiated service-account ownership and setgid/group permissions. Preserve these boundaries when configuring container UID/GID and bind mounts; verify numeric IDs and ACLs before deployment. Directory existence **does not** imply an application is installed.

## Planned deployment sequence

1. Confirm dedicated service-account UID/GID and required directory access.
2. Create/review Jellyfin Compose service and mount media libraries read-only where practical; verify local web UI.
3. Plan separate capacity for media rather than filling the 64 GiB VM disk.
4. Configure Tailscale remote access without exposing public ports.
5. Configure torrent VPN and verify fail-closed behavior, then qBittorrent.
6. Add Sonarr, Radarr, Prowlarr and optional companion applications; connect automation after isolation works.

## Design decisions

- **Dedicated VM:** keeps media workloads separate from other household services.
- **Dedicated service accounts:** limits cross-service write access.
- **Separate configuration and library paths:** simplifies backup, replacement, and storage expansion.
- **Tailscale plus distinct torrent VPN:** remote administration and outbound download privacy have different requirements.
- **Practical media safety:** standard isolation, permissions and cautious handling of untrusted downloads instead of maintaining a bespoke EPUB security research project.

This is a sanitized progress record, not a ready-to-run Compose file. No credentials, internal IPs, account names or private keys are included.
