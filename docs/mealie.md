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

## October 13, 2026 — scheduled backup and recovery verification

The scheduled 3:00 AM cron run was confirmed in the application backup log. Archive `mealie-2026-10-13_03-00-01.tar.gz` was created, the Mealie container restarted, the Pi rsync transfer completed, and the workflow logged completion at 3:00:03 AM CDT. Earlier manual archives on VM and Pi had matching SHA-256 digests and passed archive-read checks. The remote 30-day pruning command ran, but deletion of an aged file has not been observed.

A Proxmox backup of VM 101 taken at 01:44:44 was restored to temporary isolated VM 102 with its network device disconnected to prevent IP conflicts. The restored Debian system booted, Docker and Mealie were healthy, and SQLite `PRAGMA integrity_check` returned `ok`. User/recipe tables and count queries were inspected. **This verifies VM-image recovery, not a restore from the Pi application archive.** VM 102 was retained temporarily for cleanup; do not reconnect it to the LAN while the original VM is active.

## October 13, 2026 — Uptime Kuma monitoring

Dedicated unprivileged Debian LXC with native Node.js and Uptime Kuma under systemd. Service startup after container reboot and private Tailscale access from cellular were verified; no public port forwarding. Internal addresses and account names are omitted.

Mealie HTTP availability monitor checks the application once per minute. The nightly backup Push monitor expects a report within 93,600 seconds (26 hours). The orchestration script reports UP via curl only after the application backup and Pi rsync both succeed. Its Bash ERR trap attempts to report DOWN if the orchestration fails. The Push URL is stored in a root-readable-only config file separate from the script; no token or credential is committed.

A standalone simulated DOWN push changed the monitor status and triggered a successful Gmail SMTP email notification to an iPhone; a subsequent UP push restored the monitor. Bash syntax validation passed. **Not yet verified:** an intentionally failed real backup/transfer triggering the ERR trap, the next scheduled run sending its own push, actual aged-file deletion, and full restore of an application archive. The manual UP push is a connectivity test, not evidence that a scheduled backup completed.

## October 13, 2026 — ingredient parsing troubleshooting

**Symptom:** Bulk URL recipe imports succeeded and preserved recipe text, but very few ingredients automatically matched a food or measurement unit; manual parsing was required for nearly every ingredient.

**Diagnosis:** In Mealie's administration UI, **Manage Data → Foods** contained only a manually created Butter entry, and **Manage Data → Units** was empty. The empty reference datasets—not missing recipe content—were the likely cause of poor matching.

**Resolution:** Used the built-in **Seed** action and selected English for each dataset. The Foods dataset gained approximately **580 entries** and Units gained **24 entries**. No direct SQLite changes, container changes, or redeployment were necessary.

**Validation:** Reparsed ingredients in an existing imported recipe. **Most foods and units were then matched automatically**; only a few required manual verification. Existing imported recipes may need individual reparsing; seeding does not establish that all previously imported records were reprocessed automatically. This greatly reduces manual work for subsequent bulk URL imports.

**Operational takeaway:** Before bulk-importing a large recipe collection, verify that Mealie's Foods and Units reference datasets are populated. Review imported recipes for accurate quantities, food mappings, and units even when the importer reports success.
