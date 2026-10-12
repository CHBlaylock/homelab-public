# Planned four-bay SSD NAS — TrueNAS

**Status: design selected; equipment not yet ordered, installed, or tested (October 2026).**

## Purpose and architecture

A physically separate SSD NAS will provide shared household storage and versioned, off-host backups of the existing Raspberry Pi personal cloud and relevant Proxmox workloads. The Pi's current NVMe remains primary for Nextcloud and Immich; Proxmox remains the compute host (including planned Jellyfin). The NAS is **not** an additional Pi or a Proxmox VM.

A separate HDD NAS is planned later for another backup tier, ideally weekly with longer retention. Mirrors and snapshots do not replace an independent backup; test restores before treating this design as protection.

## Selected planned equipment

| Component | Plan | Estimated cost |
| --- | --- | ---: |
| Diskless NAS | Yxk Zero1 Pro, four SATA bays, Intel N100, advertised 8 GB DDR4, dual 2.5 GbE and two M.2 slots | $319.00 |
| Data drives | 2 × TEAMGROUP T-Force Vulcan Z 2 TB, 2.5-inch SATA SSD; listing model `T253TZ002T0C101`, advertised TLC (verify exact SKU/revision) | $477.98 |
| NAS OS | TrueNAS Community Edition, Linux/x86-64 | $0 |
| **Initial estimate** | Before tax, shipping, optional RAM, or contingency boot drive | **$796.98** |

Prices are user-observed planning figures, **not verified live offers**. No purchases have been confirmed. Verify drive compatibility, exact NAND/endurance/warranty, seller, dimensions and airflow before ordering. Custom rack has a maximum 10-inch internal width; physical fit and service clearances remain unverified.

## Intended boot and storage layout

- **Boot:** reported built-in 64 GB eMMC, dedicated to TrueNAS; owners have documented TrueNAS installation on the Zero1 Pro. eMMC installation may require a workaround. This is a **plan, not verification on our unit**. If it proves unreliable, consider a separate M.2 NVMe boot drive.
- **Data:** first two 2 TB SATA SSDs in a **two-way ZFS mirror**, roughly 2 TB nominal usable before formatting and metadata overhead.
- **Remaining two SATA bays:** empty initially. Both M.2 slots remain free if eMMC boot succeeds.
- **Memory:** advertised 8 GB meets TrueNAS minimum for basic use; evaluate a 16 GB upgrade based on actual needs and compatibility.
- **Network:** use a single 2.5 GbE connection initially. Dual 2.5 GbE ports do not make a single transfer 5 Gb/s; switch, client NICs and cabling must support negotiated speeds.

## Planned expansion

1. Start with two mirrored 2 TB drives; establish and test backups.
2. When affordable, acquire **two 4 TB SATA SSDs**. With a verified independent backup, replace **one mirror member at a time**, wait for resilver and healthy pool status after each, then expand the mirror as needed. Result: approximately 4 TB nominal usable. Do **not** remove both original members simultaneously.
3. After safe removal and data sanitization, repurpose the original 2 TB drives (possibly as gaming-PC storage).
4. Later add a second **4 TB + 4 TB mirrored vdev** to the same pool for approximately 8 TB nominal usable total. A second mirror increases capacity but does not make the pool immune to arbitrary two-drive failures.

## Planned configuration and validation

1. Confirm exact NAS hardware revision, firmware/BIOS access, eMMC visibility and boot order.
2. Confirm SATA disks appear individually, Ethernet controllers bind to Linux drivers, cooling is adequate, and TrueNAS installation works.
3. Install TrueNAS on a **separate boot device**, never on either data SSD. Back up the TrueNAS configuration securely after setup.
4. Create mirrored ZFS pool, datasets, SMB/NFS shares, access controls, snapshots, SMART monitoring, scrub schedules and alerts.
5. Configure consistent, application-aware backups for Nextcloud/Immich (including databases) and Proxmox VM/configuration backups. A copied live database directory alone is not a reliable application backup.
6. Verify restores, capacity headroom and snapshot retention. Protect backup credentials; avoid exposing NAS administration directly to the internet.
7. Later configure independent HDD-NAS backup/replication and test restore from that tier.

## Reference

Community TrueNAS installation discussion for this model: https://forums.truenas.com/t/unit-review-scale-25-10-on-the-yxk-iron-cow-zero1-pro/66326

Third-party reports inform feasibility but do not guarantee identical hardware or firmware in the unit ultimately purchased.
