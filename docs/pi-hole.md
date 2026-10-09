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
