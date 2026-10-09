# Homelab Equipment Inventory

This inventory distinguishes equipment in use from future projects. Retail product names and model numbers are public; private network addresses, hostnames, credentials, serial numbers, and device identifiers are not included.

## Pi-hole — dedicated DNS appliance (in use)

| Component | Exact product / description |
| --- | --- |
| Case | Raspberry Pi 3 aluminum mini tower case with cooling fan and color-changing ambient light (silver/dark gray) |
| Board | Raspberry Pi 3 Model B+ (3B+) |
| Boot storage | SanDisk 32GB High Endurance Video microSDHC card with adapter (C10, U3, V30; SDSQQNR-032G-GN6IA) |
| Power | 5V 2.5A Micro USB power supply adapter for Raspberry Pi 3 B+ |

**Role:** Dedicated Pi-hole DNS filtering appliance, with Tailscale for private network access. This is a separate physical device from the Pi-cloud.

## Pi-cloud — personal cloud and integrated photo frame (in use)

| Component | Exact product / description |
| --- | --- |
| Case/display | FREENOVE Raspberry Pi 5 Case Kit Pro — dual M.2 NVMe slots, five PWM ARGB fans, CPU tower cooler, integrated 4.3-inch touchscreen, stereo speakers, 3.5 mm audio |
| Board | Raspberry Pi 5 8GB, model SC1112, 64-bit quad-core Arm Cortex-A76 |
| Boot storage | SanDisk 32GB High Endurance Video microSDHC card with adapter (C10, U3, V30; SDSQQNR-032G-GN6IA) |
| Main storage | Predator GM7000 2TB M.2 2280 NVMe SSD, PCIe Gen4 x4, DRAM cache, model BL.9BWWR.106 |
| Power | 27W GaN USB-C PD power supply for Raspberry Pi 5 with on/off switch |

The **digital photo frame is integrated into the Pi-cloud case** using its built-in 4.3-inch display; it is not a separate monitor or separate Raspberry Pi.

## Other equipment

| Equipment | Status / intended role |
| --- | --- |
| Dell OptiPlex 7060 SFF | Donated; future main home server |
| Second independent Raspberry Pi 5 cloud | Planned, not yet acquired |
| 2.5-inch SSD NAS | Planned household storage and secondary backup target |
| HDD NAS | Planned additional weekly backup target |
| Managed switch / compact rack infrastructure | Under evaluation; not yet selected |

## Scope and privacy

The listed specifications are based on the recorded purchased-product descriptions, not independent verification of every manufacturer's marketing claim. The public inventory intentionally excludes purchase receipts, order numbers, serial numbers, private topology, device addresses, and other identifying details.

See the project documentation for Linux, Docker, Immich, Nextcloud, Pi-hole, Tailscale, and photo-frame implementation.
