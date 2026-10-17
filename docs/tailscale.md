# Tailscale remote access

## Architecture

The homelab uses Tailscale for private administration and self-hosted service access without exposing management ports through the household router.

- A dedicated Raspberry Pi hosts DNS filtering and participates in Tailscale.
- A separate Raspberry Pi personal-cloud node uses Tailscale for remote application access.
- The Dell OptiPlex Proxmox hypervisor was added to the existing tailnet in October 2026.
- The hypervisor was **not** configured as an exit node or subnet router.
- The isolated training VM bridge has no physical uplink or default gateway; connecting the hypervisor to Tailscale did not give the isolated VM internet connectivity.

## Proxmox host deployment

On the Debian-based hypervisor:

```bash
curl -fsSL https://tailscale.com/install.sh | sh
tailscale up --accept-dns=false
tailscale status
systemctl is-enabled tailscaled
```

The `--accept-dns=false` setting preserves the host's existing DNS configuration. The host joined the existing tailnet, and the service was verified enabled at boot.

**Validation:** A mobile phone with Wi-Fi disabled successfully loaded the Proxmox management interface over cellular data using its Tailscale address and the Proxmox HTTPS management port. This tests a genuinely off-LAN path rather than relying on two devices already on the same local network.

An informational status message indicated that other peers advertised routes while this host was configured not to accept them. That is appropriate for a host already directly attached to the home LAN.

## Routing troubleshooting

A home-based Windows client previously experienced a routing conflict from accepting an advertised subnet route to the same LAN it was physically connected to. The client-side fix was:

```bash
tailscale set --accept-routes=false
```

## Security

No router port forwarding was needed. Tailnet ACLs/grants and host authentication remain important controls. Tailscale does not replace Proxmox authentication, and its presence on the hypervisor does not automatically expose or route isolated guest networks.

Actual addresses, hostnames, tailnet identities, device identifiers, and authentication material are intentionally omitted.

## Media server guest (verified 2026-10-17)

A separate Ubuntu media-server VM joined the existing tailnet. Using an iPhone on cellular data, remote web access was confirmed for Jellyfin, Audiobookshelf, Navidrome, and Calibre-Web Automated. Jellyfin video playback also succeeded. No router port forwarding was needed. This inbound remote-access path is separate from the dedicated outbound VPN used for torrent traffic. Exact addresses and SSH usernames are retained only in the private repository.
