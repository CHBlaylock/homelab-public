# Networking

## Access model

The homelab uses a simple access boundary:

- **Local:** LAN
- **Remote:** Tailscale

Tailscale provides private remote access without requiring public port forwarding.

## Troubleshooting example

A Windows client experienced competing LAN and Tailscale routes for the home subnet. The lower-metric Tailscale route caused local traffic to use the wrong interface.

The resolution was to disable route acceptance on the permanently home-based client:

```
tailscale set --accept-routes=false
```

Actual addresses, hostnames, and tailnet identifiers are intentionally omitted from this public repository.
