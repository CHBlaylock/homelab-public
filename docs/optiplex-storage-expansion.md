# OptiPlex SFF hardware inspection and storage expansion plan

**Status:** Existing hardware inspected; SSD expansion, networking upgrade and external NAS tiers are planned, not installed.

## Verified hardware

- Dell OptiPlex 7060 SFF, Intel Core i7-8700 (6 cores / 12 threads), 24 GB RAM.
- 512 GB-class NVMe SSD used for the Proxmox host and initial virtual machines.
- Intel chipset SATA AHCI controller and integrated Gigabit Ethernet.
- Original Dell **200 W** small-form-factor power supply.
- Original Dell SATA power harness with one standard 15-pin SATA power connector and one slimline optical-drive power connector.

## Internal expansion assessment

Physical inspection identified three conventional SATA data connectors, including the port occupied by the removable optical drive. The optical drive can be removed to reclaim space for a custom SSD mounting assembly.

**Design target:** Up to three 2.5-inch SATA SSDs, subject to controller support, power delivery, cable routing, mechanical fit and cooling. The original NVMe remains available for Proxmox.

Power planning requires more than counting connectors: the OEM motherboard-side drive-power header and wiring must be verified for the combined drive load. A compatible OEM replacement or properly rated splitter is under evaluation; no cable has been selected or installed. The PSU's 200 W total rating does not independently establish the capacity of the SATA power branch.

PCIe expansion may support an additional NVMe drive and a 2.5 GbE adapter. Slot capabilities and mechanical fit have not yet been confirmed. NVMe uses PCIe, not ordinary SATA data connectors.

## Planned architecture

- Existing Raspberry Pi personal-cloud nodes remain separate from the virtualization host.
- The OptiPlex hosts Proxmox and is being evaluated for additional household workloads and storage.
- A future SSD-based NAS will provide off-host, versioned secondary backups.
- A future HDD-based NAS will receive an additional weekly backup tier.
- A compact custom rack with up to 10-inch internal width and a 2.5 GbE switch is under design.
- Restore testing is part of the backup acceptance criteria. Local VM snapshots do not protect against failure of the host's physical drive.

No drives, NAS devices, power adapters or network upgrades described here have been deployed yet. This public record omits internal addresses, device identifiers and OEM harness traceability numbers.
