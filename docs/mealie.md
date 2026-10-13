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
| Nightly orchestration | Cron starts application backup at 3 AM server local time, then invokes off-host transfer only after backup command succeeds | Configured; manual end-to-end run passed; scheduled cron execution and restore test pending |
| Disaster recovery | Restore archive into a clean disposable VM and validate recipes/accounts | Not yet tested |

The two backup destinations provide different hardware failure domains, but neither substitutes for a tested restore or a later dedicated backup NAS. Pi archives older than 30 days are now pruned after successful transfer; this cleanup was manually tested.

## Engineering decisions

- **Dedicated VM instead of mixing services into the personal-cloud Pi:** separates compute and failure domains.
- **SQLite on the VM's local disk instead of an ordinary network share:** avoids introducing remote-filesystem locking risks for the live database.
- **Tailscale instead of internet-facing port forwarding:** limits exposure of the web service.
- **Application-aware archive instead of copying a live SQLite directory:** stops the service for a consistent data snapshot.
- **Sequential backup then rsync instead of two fixed-offset cron jobs:** avoids racing an unfinished backup.
- **Off-host Pi copy before NAS purchase instead of waiting for new hardware:** provides a second physical copy using existing equipment.

## Next verification

Manual nightly workflow passed on October 13, 2026: matching named 65 KB archive on VM and Pi, older archive retained, and container reported healthy. Next: verify first scheduled cron run, archive integrity and restore in an isolated environment. Future improvement: migrate off-host storage to the planned dedicated TrueNAS appliance and apply independent retention/integrity checks.

## October 13, 2026 manual end-to-end test

Executed the nightly orchestration script manually. Confirmed matching new archive `mealie-2026-10-13_02-36-29.tar.gz` (65 KB displayed) in both local and Pi backup directories; prior `02-12-18` archive remained on both. Docker reported the Mealie container `Up (healthy)` after restart. **This verifies the manual workflow, not yet the cron-triggered run, checksum equality, archive extraction, or a full restore.**

## October 13, 2026 — Pi retention configured

Updated `/usr/local/sbin/sync-mealie-to-pi.sh` to perform a remote `find` restricted to `/mnt/nvme/backups/mealie`, matching `mealie-*.tar.gz`, `-mtime +29`, and `-delete`, **only after rsync succeeds**. Syntax check and manual transfer/retention invocation were confirmed successful. A read-only precheck found no files old enough to delete; actual deletion of aged files has not yet been observed. Future scheduled run and isolated restore test remain pending.
