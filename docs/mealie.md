# Mealie on Proxmox: self-hosted recipe management

**Updated:** October 2026. Sanitized technical deployment record; no private addresses, host-specific SSH credentials, or secrets.

## Architecture

A dedicated Debian VM on a Proxmox VE hypervisor runs Mealie in Docker Compose. Mealie's SQLite database, recipes, media, and application state persist in a bind-mounted data directory. Local household access uses the LAN, while remote access uses Tailscale with no public port forwarding. Two household accounts and cellular remote access have been verified.

## Backup layers

| Layer | Method | Status |
| --- | --- | --- |
| VM image | Daily Proxmox snapshot backup with ZSTD compression; retain three | Configured; manual backup verified |
| Application data | Shell script stops Mealie, archives Compose configuration and persistent data, then restarts service; retain seven local archives | Manual backup and archive contents verified |
| Off-host | rsync over SSH with a dedicated Ed25519 key to a separate Raspberry Pi 5 NVMe drive | Manual transfer and passwordless SSH verified |
| Nightly orchestration | Cron starts application backup at 3 AM server local time, then invokes off-host transfer only after backup command succeeds | Configured; complete end-to-end test pending |
| Disaster recovery | Restore archive into a clean disposable VM and validate recipes/accounts | Not yet tested |

The two backup destinations provide different hardware failure domains, but neither substitutes for a tested restore or a later dedicated backup NAS. Remote archives currently accumulate; separate retention management is future work.

## Engineering decisions

- **Dedicated VM instead of mixing services into the personal-cloud Pi:** separates compute and failure domains.
- **SQLite on the VM's local disk instead of an ordinary network share:** avoids introducing remote-filesystem locking risks for the live database.
- **Tailscale instead of internet-facing port forwarding:** limits exposure of the web service.
- **Application-aware archive instead of copying a live SQLite directory:** stops the service for a consistent data snapshot.
- **Sequential backup then rsync instead of two fixed-offset cron jobs:** avoids racing an unfinished backup.
- **Off-host Pi copy before NAS purchase instead of waiting for new hardware:** provides a second physical copy using existing equipment.

## Next verification

Run the complete nightly workflow once, verify matching local and off-host archives, inspect logs and container health, then test a restore in an isolated environment. Future improvement: migrate off-host storage to the planned dedicated TrueNAS appliance and apply independent retention/integrity checks.
