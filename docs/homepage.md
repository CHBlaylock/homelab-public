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

 network reservation/startup checks; and runtime memory review.

## Proxmox read-only widget — verified October 2026

Added a **Virtualization → Proxmox** Homepage widget. It successfully displays live VM and LXC counts and host CPU/memory utilization, alongside the existing Pi-hole widget.

Authentication uses a dedicated Proxmox VE account assigned the propagated `PVEAuditor` role, plus a **privilege-separated API token** with its own propagated `PVEAuditor` permissions. The API token ID and secret are supplied through Homepage environment variables in the local protected environment file; no token secrets are published.

Sanitized configuration:

```yaml
- Virtualization:
    - Proxmox:
        icon: proxmox.svg
        href: https://PROXMOX_LAN_IP:8006
        description: Proxmox VE virtualization host
        widget:
          type: proxmox
          url: https://PROXMOX_LAN_IP:8006
          username: "{{HOMEPAGE_VAR_PROXMOX_TOKEN_ID}}"
          password: "{{HOMEPAGE_VAR_PROXMOX_TOKEN_SECRET}}"
          node: PROXMOX_NODE_NAME
```

A read-only API connectivity test returned HTTP 200, and the widget was visually verified after restarting Homepage. The connectivity test temporarily bypassed TLS certificate validation for the host's self-signed certificate; certificate trust should be hardened separately.

## Uptime Kuma monitoring widget — verified October 2026

Created a **Homelab Status** page in Uptime Kuma with the existing application and nightly-backup monitors. Verified that the status page was accessible without signing in over the private network, then added it to Homepage. No port forwarding or public internet access was enabled.

Sanitized widget configuration:

```yaml
- Monitoring:
    - Uptime Kuma:
        icon: uptime-kuma.svg
        href: http://UPTIME_KUMA_LAN_IP:3001
        description: Homelab service monitoring
        widget:
          type: uptimekuma
          url: http://UPTIME_KUMA_LAN_IP:3001
          slug: homelab
```

After restarting Homepage, the widget displayed **2 sites up, 0 sites down, 83% historical uptime**, while Pi-hole and Proxmox continued functioning. Status pages are unauthenticated to reachable clients; monitor names and descriptions should be selected accordingly.

## Mealie widget — verified October 2026

Mealie's Homepage widget is working using a dedicated API token stored in the local protected environment file, outside version control. The public documentation intentionally omits the real address and token.

Sanitized widget configuration:

```yaml
- Applications:
    - Mealie:
        icon: mealie.svg
        href: http://MEALIE_LAN_IP:MEALIE_PORT
        description: Recipe management and meal planning
        widget:
          type: mealie
          url: http://MEALIE_LAN_IP:MEALIE_PORT
          key: "{{HOMEPAGE_VAR_MEALIE_API_TOKEN}}"
          version: 2
```

### Compatibility note

The default legacy API endpoint returned the HTML frontend rather than statistics, producing an invalid-data error. Testing confirmed that the newer household statistics endpoint returned JSON when authenticated. Setting `version: 2` selected the compatible endpoint and fixed the widget. The live widget was verified displaying recipe, user, category, and tag counts. No token or private network address is published.

## Personal Cloud links — verified October 2026

Added a dedicated **Personal Cloud** section with links to the self-hosted photo library (Immich), cloud storage (Nextcloud), and Tailscale device administration. The links render correctly in the dashboard. These are navigation links rather than health checks; uptime monitoring is configured separately in Uptime Kuma.

The default Developer, Social, and Entertainment bookmark groups were removed to keep the dashboard focused on homelab services. Homepage is enabled for automatic startup, and its local environment file is protected with root ownership and mode `600`. The dashboard has working Pi-hole, Proxmox, Uptime Kuma, and Mealie widgets. Private LAN addresses are intentionally omitted from this public document.
