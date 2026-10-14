# Homepage dashboard — deployed October 2026

**Status: live and verified.** Homepage 2.5.0 is deployed in an unprivileged Debian 13 LXC on Proxmox, with a Pi-hole v6 statistics widget. Local access and Tailscale remote access from an iPhone on cellular data were verified without public port forwarding.

## Implementation

Homepage was built from source using Node.js 22, pnpm, and a production Next.js build. A dedicated systemd service runs it at startup. Tailscale runs inside the container using a passed-through TUN device. Integration secrets are kept in a protected local environment file outside GitHub.

Sanitized Pi-hole widget configuration:

```yaml
- Network Services:
    - Pi-hole:
        icon: pi-hole.svg
        href: http://PIHOLE_LAN_IP/admin/
        description: Network DNS and ad blocking
        widget:
          type: pihole
          url: http://PIHOLE_LAN_IP
          version: 6
          key: "{{HOMEPAGE_VAR_PIHOLE_API_PASSWORD}}"
```

Pi-hole v6 supports a dedicated app password generated from its web interface. The password is stored in a root-owned, mode-0600 environment file loaded by the Homepage systemd service. No passwords, API keys, real LAN addresses, tailnet addresses, or private hostnames are published.

## Troubleshooting lesson

An initial Homepage authentication failure (HTTP 401 from the Pi-hole API) was traced to an incorrect saved credential. Testing authentication locally against Pi-hole and then using the exact credential loaded from Homepage isolated the problem. After correcting the environment file and restarting Homepage, the widget displayed query totals, blocked query statistics, and gravity-domain counts. Network routing and Tailscale were not the cause; no Pi-hole configuration change was needed.

## Next steps

Planned, not yet completed: Proxmox, Uptime Kuma, and Mealie dashboard integrations; network reservation/startup checks; and runtime memory review.
