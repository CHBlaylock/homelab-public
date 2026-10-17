# Media server deployment (VM 104)

Status: **Core media stack deployed and remote access verified**, updated 2026-10-17.

## Objective and current state
Dedicated Ubuntu 24.04 LTS Proxmox VM hosting six Docker Compose services: Jellyfin, Audiobookshelf, Calibre-Web Automated, Navidrome, Gluetun and qBittorrent. Docker starts on boot and all services use `unless-stopped` restart policies. A full reboot recovery test is still pending.

| Service | Role | Current verification |
| --- | --- | --- |
| Jellyfin | Movies and television | Libraries configured; local and remote video playback verified |
| Audiobookshelf | Audiobooks and podcasts | Libraries configured; remote interface verified |
| Navidrome | Music | Music mount configured; remote interface verified |
| Calibre-Web Automated | Ebooks | Ebook library configured; remote interface verified |
| Gluetun | Dedicated WireGuard VPN gateway | Healthy, VPN egress and fail-closed behavior tested |
| qBittorrent | Download client | Running through Gluetun's network namespace |

Remote application access is via Tailscale on the media VM, verified using a phone on cellular data. No router port forwarding is required. Remote web access was verified for all four media apps, but only Jellyfin video playback was tested.

## Storage layout
```text
/opt/media-stack/compose.yaml
/opt/media-stack/config/             # persistent application state
/srv/media/downloads/{incomplete,complete}
/srv/media/quarantine
/srv/media/validation/{pending,approved}
/srv/media/library/{movies,tv,music,audiobooks,podcasts,ebooks}
```
Library mounts are read-only for playback apps where appropriate. The library currently resides on the VM's ext4 root filesystem, which has limited space; a separate NAS is planned for permanent media storage. Preserve container paths when migrating storage and prevent silent writes to the VM root disk if a NAS mount fails.

## Video intake
`validate-media` performs a ClamAV scan and video-stream check, then stages files for `approve-media`. Approved files can be imported using `import-movie` or `import-tv`; the latter organizes files by show and season. The movie path passed an end-to-end synthetic media test, including Jellyfin playback. The new TV importer has passed syntax and permissions checks but has not yet had a full end-to-end test. Temporary copies consume disk space.

## Network and security decisions
- Tailscale handles private remote application access; it is distinct from the outbound torrent VPN.
- qBittorrent shares Gluetun's network namespace and is bound to the tunnel interface. The VPN firewall and host isolation rules were tested; firewall rules persist through systemd.
- No WireGuard private keys, internal IP addresses, host usernames, passwords, or tokens are included in public documentation.
- Standard access controls, scanning, and format validation provide baseline risk reduction; they do not guarantee downloaded content is safe.

## Limitations and next steps
The guest currently has only a virtual VGA adapter; Jellyfin uses software transcoding. Intel GPU passthrough for hardware transcoding can be revisited later. A full reboot recovery test, TV import test, optional media automation services, and NAS migration remain pending.

**Decision rationale:** Dedicated VM simplifies isolation and recovery; separate service identities and library mounts reduce cross-service write access; private Tailscale remote access avoids public port exposure; separate outbound VPN isolates torrent traffic; stable container paths simplify NAS migration.
