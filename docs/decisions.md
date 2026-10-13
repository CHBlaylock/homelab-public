# Homelab architecture decision log

**Last updated:** October 2026. Decisions are labeled deployed or planned. This is a rationale record, not proof that planned hardware has been ordered or tested.

| Status | Decision | One-line rationale and alternative |
| --- | --- | --- |
| Deployed | BE6500 remains the household router/firewall/DHCP server | Keeps a stable network edge rather than turning the Pi-hole device into a router. |
| Deployed | Dedicated wired Pi 3 B+ for Pi-hole | Keeps household DNS independent from Pi cloud applications and Proxmox restarts. |
| Deployed | Tailscale rather than exposed router ports | Provides private remote access without directly opening management services to the internet. |
| Deployed | Pi 5 NVMe for active cloud storage | Offers greater capacity and performance than storing application data on microSD. |
| Deployed | Separate Immich and Nextcloud stacks | Uses Immich for photos/videos and Nextcloud for documents rather than forcing both jobs into one app. |
| Deployed | Integrated photo frame uses the existing Pi display | Reuses the case screen and an Immich album rather than adding a second photo device/library. |
| Deployed | Graphical Raspberry Pi OS for the display Pi | Supports the frame display/orientation needs that were problematic with the initial Lite setup. |
| Deployed | Donated OptiPlex with Proxmox | Reuses capable available hardware instead of purchasing a new virtualization server. |
| Deployed | Isolated Ubuntu lab bridge without physical uplink | Reduces the risk of experiments reaching household devices instead of bridging lab VMs onto the main LAN. |
| Deployed | Baseline Ubuntu VM snapshot | Makes lab rollback convenient; independent backups remain necessary. |
| Deployed | Tailscale on Proxmox with DNS acceptance disabled | Enables remote management without overriding the host resolver or routing the isolated lab. |
| Planned | Separate personal Pi clouds | Gives each person's files and services their own instance instead of combining everything on one Pi. |
| Planned | Dedicated SSD NAS rather than Proxmox-hosted storage | Keeps off-host backups and shared files independent of hypervisor maintenance/failure. |
| Planned | 2.5 GbE networking for new equipment | Provides a practical bandwidth upgrade over 1 GbE without immediately paying for 10 GbE. |
| Planned | Custom compact rack, maximum 10-inch internal width | Fits the intended footprint and user-fabricated supports rather than using a full-size rack. |
| Selected, not purchased | Yxk Zero1 Pro over a Pi NAS, CWWK DIY system, or the Yxk two-bay | Its x86 platform, four SATA bays, and dual 2.5 GbE better fit TrueNAS and staged expansion at the observed price. |
| Selected, not installed | TrueNAS Community Edition over proprietary NAS firmware or OMV | Integrated ZFS administration, snapshots and replication fit the intended backup/storage architecture. |
| Selected, not purchased | TEAMGROUP Vulcan Z 2 TB advertised TLC over TEAMGROUP QX QLC or a less-established option | Prefers advertised TLC and familiar vendor support despite a modestly higher initial price; exact SKU must be checked. |
| Planned | Two 2 TB drives before larger drives | Reduces the initial cash outlay while allowing future capacity upgrades. |
| Planned | ZFS two-disk mirror over a stripe | Maintains availability after a single data-drive failure instead of maximizing unprotected capacity. |
| Planned, verify on arrival | Use internal 64 GB eMMC as TrueNAS boot | Preserves both M.2 slots and avoids buying a boot SSD if the reported installation method works on this unit. |
| Planned | Replace the 2 TB mirror members one at a time with 4 TB drives | Allows capacity expansion while preserving the pool and eventually freeing the original drives for reuse. |
| Planned | Add a second pair of mirrored 4 TB drives | Grows storage in stages without purchasing four 4 TB SSDs initially. |
| Planned | Later HDD NAS as an additional backup tier | Creates another physical copy beyond the primary Pi storage and SSD NAS, rather than relying on ZFS redundancy. |
| Planned | Jellyfin processing on Proxmox, media stored on NAS | Places compute/transcoding on the server while leaving the NAS optimized for storage. |

| Deployed | Dedicated Mealie Debian VM on Proxmox | Isolates household recipe management from Pi cloud workloads instead of colocating everything on the Pi. |
| Deployed | Mealie remote access through Tailscale | Enables household access over cellular without exposing the application through router port forwarding. |
| Deployed | Application-consistent Mealie archive with off-host Pi copy | Stops SQLite-backed app before archiving and stores a second copy on separate hardware instead of relying only on same-host VM backups. |
| Configured; full test pending | Sequential nightly Mealie backup then SSH/rsync transfer | Avoids a fixed-delay transfer racing an unfinished backup; full scheduled workflow and restore still need verification. |

## Documentation rule

For future meaningful choices, record the **decision**, its **one-sentence reason versus an alternative**, and the **implementation status**. Prefer measurable constraints: cost, capacity, reliability, compatibility, network speed, power, privacy, or independence. Update the status only after an order, installation, or test is confirmed.

See [NAS storage design](nas-storage.md) for the selected NAS hardware, uncertainties, and verification steps.
