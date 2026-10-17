# Media server deployment (VM 104)

Status: **Core media stack deployed; torrent automation enabled and awaiting scheduled-run validation**, updated 2026-10-17.

## Objective and current state

Dedicated Ubuntu 24.04 LTS Proxmox VM hosting Jellyfin, Audiobookshelf, Calibre-Web Automated, Navidrome, Gluetun and qBittorrent, with Sonarr and Radarr integrated into the download workflow. Docker starts on boot and deployed Compose services use unless-stopped restart policies. A full reboot recovery test is still pending.

| Service | Role | Current verification |
| --- | --- | --- |
| Jellyfin | Movies and television | Libraries configured; local and remote playback verified; Reacher S02E07 appeared after correcting file ACLs and rescanning |
| Audiobookshelf | Audiobooks and podcasts | Libraries configured; remote interface verified |
| Navidrome | Music | Music mount configured; remote interface verified |
| Calibre-Web Automated | Ebooks | Ebook library configured; remote interface verified |
| Gluetun | Dedicated WireGuard VPN gateway | Healthy; VPN egress and fail-closed behavior tested |
| qBittorrent | Download client | Runs in Gluetun's network namespace; approved torrent start tested |
| Sonarr / Radarr | TV and movie management | Integrated with categorized qBittorrent downloads and import workflows |

Remote application access is via Tailscale on the media VM, verified using a phone on cellular data. No router port forwarding is required. Remote web access was verified for all four media apps, and Jellyfin playback was tested.

The media-torrent-automation systemd timer is enabled at a 10-minute interval. Its coordinated service runs the existing metadata inspector followed by the approved-download controller. The initial manual coordinated run succeeded; the first timer-triggered end-to-end run is still awaiting verification. The controller started 12 eligible approved torrents in a manual run. A manually stopped Reacher torrent remained stopped during the next controller run while another approved torrent started. Validate this behavior across scheduled runs before considering manual-pause protection complete.

The inspector state records screening results and authorization. Rejected and unverified records remain unauthorized. Automatic retries for unverified metadata and automatic alternative-release searches are not yet implemented.

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
The existing validation flow uses ClamAV and ffprobe, stages files for approval, then imports approved media into the appropriate library. The TV importer successfully moved Reacher S02E07 into the TV library, triggered a Sonarr rescan, and the episode appeared in Jellyfin after a read ACL was added for Jellyfin's service UID. Both TV and movie importers now set Jellyfin read access after moving a file. The importer account was tested and can apply the ACL. Future real imports still need observation to verify the fix end to end. Temporary copies consume disk space.

## Network and security decisions
- Tailscale handles private remote application access; it is distinct from the outbound torrent VPN.
- qBittorrent shares Gluetun's network namespace and is bound to the tunnel interface. The VPN firewall and host isolation rules were tested; firewall rules persist through systemd.
- No WireGuard private keys, internal IP addresses, host usernames, passwords, or tokens are included in public documentation.
- Standard access controls, scanning, and format validation provide baseline risk reduction; they do not guarantee downloaded content is safe.

## Limitations and next steps
The guest currently has only a virtual VGA adapter; Jellyfin uses software transcoding. Intel GPU passthrough for hardware transcoding can be revisited later. Full reboot recovery and NAS migration remain pending. The 10-minute torrent timer is enabled, but its next timer-triggered run still needs validation. Unverified metadata retry and alternative-release search remain planned. Confirm manual-stop behavior over repeated scheduled runs before treating it as fully validated.

**Decision rationale:** A dedicated VM simplifies isolation and recovery; separate service identities and library mounts reduce cross-service write access; private Tailscale remote access avoids public port exposure; a separate outbound VPN isolates torrent traffic; metadata approval gates automatic starting; explicit per-torrent start tracking is intended to respect manual stops; stable container paths simplify NAS migration.
