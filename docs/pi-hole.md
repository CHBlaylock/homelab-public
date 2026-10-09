# Pi-hole — Raspberry Pi 3 B+ DNS Appliance

## Hardware

The Pi-hole is a **dedicated Raspberry Pi 3 Model B+**, separate from the Raspberry Pi 5 personal-cloud appliance.

- Raspberry Pi 3 Model B+ (3B+)
- Aluminum mini tower case with cooling fan and color-changing ambient light
- SanDisk 32GB High Endurance Video microSDHC card
- 5V 2.5A Micro USB power supply adapter

These are the recorded purchased components. See [Equipment](equipment.md) for the full inventory.

## Purpose

The Pi runs Pi-hole for network-wide DNS filtering. The private deployment also uses Tailscale for secure remote connectivity. Raspberry Pi OS Lite is used for this headless network appliance.

## Skills and architecture

- Dedicated Linux DNS service deployment
- DNS filtering and blocklist maintenance
- Router/DHCP DNS integration
- Tailscale-based private access
- Service troubleshooting and maintenance

## Privacy

This document intentionally excludes real hostnames, local and remote IP addresses, tailnet identifiers, authentication information, device serial numbers, and detailed routing settings.

## Centralized DNS for LAN and Tailscale

The Raspberry Pi 3 B+ is **wired to the home router over Ethernet** and runs Pi-hole as the central DNS filtering service.

- **Home network:** The router advertises Pi-hole as a DNS server through DHCP. Devices using the router-provided DNS settings automatically send their DNS queries to Pi-hole without manual setup on each device.
- **Remote devices:** Tailscale is configured to make the Pi-hole DNS service available to connected devices outside the home, providing consistent DNS filtering over the private tailnet.
- **Division of responsibilities:** The router provides DHCP, routing, firewall, and Wi-Fi; the Raspberry Pi provides DNS filtering.

This is **centralized DNS configuration**, not forced interception of all DNS traffic. Devices or applications using custom DNS, encrypted DNS, or alternative resolvers may bypass filtering. Verification involves checking DNS settings and Pi-hole query logs for both LAN and Tailscale clients.

No actual IP addresses, hostnames, tailnet identifiers, or private network configuration values are included in this public description.

