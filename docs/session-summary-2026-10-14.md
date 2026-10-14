# October 14 Homelab session summary

## Homepage
Changed: Deployed and configured Homepage in Proxmox LXC, enabled startup, added working Pi-hole, Proxmox, Uptime Kuma, and Mealie widgets, and added personal cloud navigation links. Fixed Pi-hole credentials and Mealie API version compatibility. Why: one operational dashboard. Verified: live widgets and remote Tailscale access.

## Uptime Kuma
Changed: Confirmed startup service, removed duplicate unit, expanded to 10 monitors including LAN and Tailscale services and Pi-hole DNS, updated status page, and enabled Gmail alerts. Why: visibility into service and DNS outages. Verified: 10 monitors Up and Gmail test delivered.

## Independent watchdog
Changed: Pi script checks Uptime Kuma each minute, alerts after three failures, and emails on recovery; msmtp config protected. Why: monitor the monitor independently. Verified: simulated outage and recovery emails, cron execution.

## Storage health
Changed: smartmontools and Python script monitor OS and NVMe disk capacity, temperature, wear, spare, and errors every 15 minutes. Persistent state prevents duplicate alerts. Why: early warning before cloud storage failure. Verified: SMART passed, simulated alert and recovery emails, cron launch, empty alert state.

## Security and next steps
Protected credentials outside Git; used read-only API permissions and Tailscale. Next: backups, hardware/resource monitoring, certificate hardening, and 2.5 GbE networking. Cron logs confirm launch, not process completion.
