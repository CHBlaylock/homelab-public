# Media server deployment (VM 104)

**Status:** Core media services deployed; library automation and verification ongoing (October 2026).

## Architecture
A dedicated Ubuntu Proxmox VM hosts a Docker Compose media environment. Docker restart policies support service recovery; a complete reboot recovery test remains pending.

| Service | Purpose | Verification |
| --- | --- | --- |
| Jellyfin | Video library, metadata display and streaming | Local and Tailscale remote playback verified |
| Audiobookshelf | Audiobook and podcast library management | Remote interface verified; audio workflows pending |
| Navidrome | Music library and streaming | Remote interface verified; music workflow pending |
| Calibre-Web Automated | Ebook library and reading interface | Ebook imports and library display verified |
| Sonarr / Radarr | Television and movie library organization, naming and metadata coordination | Integrated with library workflow |
| Prowlarr | Media catalog and index metadata coordination | Service deployed |
| Seerr | Media request interface | Service deployed |
| Shelfmark | Ebook discovery and request interface | Service deployed |

## Storage and file handling
Applications use persistent configuration storage and organized movie, TV, music, audiobook, podcast and ebook libraries. Media is currently stored on the VM pending migration to a dedicated NAS. The future mount design must prevent silent writes to local storage when remote storage is unavailable.

Automated movie and television import scripts use release-name parsing, library organization, permissions management and scheduled Linux services. An imported TV episode was initially invisible in Jellyfin until its read ACL was corrected; import scripts now apply read access after moving media. Additional unattended import verification is ongoing.

Ebook processing uses ClamAV, file-format and structural checks, and staged validation before library import. An unprivileged approval worker and systemd timer are deployed. Several ebooks have been confirmed in the reading interface; a fresh unattended end-to-end test remains pending.

## Access and operations
Tailscale provides private remote access without router port forwarding. The VM currently uses software transcoding; GPU acceleration and storage migration remain future projects. Application backup restoration, complete reboot recovery and some scheduled-workflow tests remain pending.

**Design rationale:** isolate workloads in a VM, keep library paths stable, use explicit service permissions, automate repeatable organization and metadata tasks, and verify workflows before declaring them production-complete.

This public documentation excludes private network addresses, credentials and sensitive operational configuration.
