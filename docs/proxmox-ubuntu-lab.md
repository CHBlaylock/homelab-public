# Proxmox VE virtualization host and isolated Ubuntu lab

**Status: Implemented (October 2026).** This public portfolio record intentionally excludes private IP addresses, hardware identifiers, credentials, and internal host/domain names.

## Objective

Convert a donated small-form-factor desktop into a Proxmox virtualization host, establish a separate training network, deploy Ubuntu Server, troubleshoot installation issues, and preserve a reproducible recovery point without disrupting production home services.

## Verified hardware and platform

- Dell OptiPlex 7060 SFF, Intel Core i7-8700, 6 cores / 12 threads, 24 GB RAM, 512 GB NVMe SSD.
- Verified firmware virtualization support, storage health and configuration before replacing the existing OS.
- Installed Proxmox VE 9.2.2 (Debian 13 base) in UEFI mode; updated to kernel 7.0.14-23-pve and verified successful reboot.
- Disabled subscription-only enterprise package sources, enabled the no-subscription Proxmox repository, refreshed and upgraded packages.
- Used the default ext4/LVM-thin storage scheme: a directory pool for installation images/templates and an LVM-thin pool for virtual disks.

## Network architecture

- Primary Linux bridge connects the hypervisor to the household LAN via the physical NIC.
- Secondary Linux bridge has no physical bridge ports, host IP, or gateway and is reserved for isolated training VMs.
- The initial Ubuntu guest has a static address on a **separate private training subnet**, with no default route or DHCP service.
- A dedicated virtual firewall, DHCP/DNS services, and controlled internet access are **planned, not deployed**. Before adding routing, explicit deny policies must protect household services; NAT alone is not a security boundary.

## Ubuntu training VM

| Component | Configuration |
| --- | --- |
| Guest | Ubuntu Server 24.04.3 LTS |
| Compute | 2 vCPU, 4 GiB RAM maximum (2 GiB ballooning minimum) |
| Storage | 32 GiB VirtIO SCSI virtual disk on LVM-thin |
| Disk options | IO thread, discard, SSD emulation |
| Network | VirtIO adapter attached to isolated training bridge |
| Remote administration | OpenSSH installed; socket activation verified |
| Guest agent | Proxmox-side option enabled; package installation pending |
| Recovery | Powered-off clean baseline snapshot successfully created |

The guest was installed without internet access. Its Netplan configuration was created manually, and interface state, address and routes were verified. Guided LVM retained free capacity for future filesystem expansion exercises.

## Troubleshooting and validation

1. Resolved an initial display output issue with a power cycle, then used the firmware one-time boot menu to launch the Proxmox installer.
2. Verified storage-controller settings before the destructive hypervisor installation.
3. Completed repository migration, package upgrade and kernel reboot; recognized an upgrade changelog pager as informational rather than a failure.
4. Identified expected DHCP failure on the intentionally disconnected training bridge; completed the Ubuntu installation offline.
5. Created a persistent Netplan configuration and verified that the VirtIO NIC became configured and online without introducing a default gateway.
6. Diagnosed a boot-time `systemd-networkd-wait-online` timeout, verified interface readiness, restarted the service, and confirmed zero failed systemd units.
7. Checked the VM disk layout and free capacity; detached installation media and created a baseline snapshot.

## Current limitations and roadmap

The guest agent and Ubuntu updates remain pending because the lab is deliberately offline. Future work includes a virtual firewall with explicit network segmentation rules, package updates, guest-agent integration, Linux administration exercises, and off-host backup storage. A VM snapshot provides rollback, **not disaster recovery** against physical SSD failure.

**Skills demonstrated:** bare-metal deployment, UEFI/BIOS troubleshooting, virtualization, Linux bridges, network isolation, package repositories, systemd diagnostics, Netplan, LVM, SSH, snapshots, and technical documentation.
