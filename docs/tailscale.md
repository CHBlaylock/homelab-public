# Tailscale

Tailscale provides private remote access to homelab services.

## Design

- Local clients use the LAN.
- Remote clients use Tailscale.
- Public Internet port forwarding is not required for the intended access model.

## Troubleshooting lesson

A client that was permanently located at home had a routing conflict caused by accepting a Tailscale subnet route for the same LAN it was already connected to directly.

The client was configured with:

```
tailscale set --accept-routes=false
```

Actual addresses and tailnet identifiers are intentionally omitted.
