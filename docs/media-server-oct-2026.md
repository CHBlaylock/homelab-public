# Media server implementation: October 2026

A dedicated Proxmox Ubuntu VM now hosts a Docker Compose media stack including **Jellyfin, Sonarr, Radarr, Prowlarr, Seerr, qBittorrent, Gluetun, FlareSolverr, Calibre-Web Automated, Shelfmark, Navidrome, and Audiobookshelf**.

## Verified engineering work
- Private remote access through Tailscale, including Jellyfin access over cellular, without router port forwarding.
- Torrent traffic routed through a separate WireGuard VPN container, with egress isolation and fail-closed behavior tested.
- Automated movie/TV library importers using Linux services/timers, release-name parsing, sample exclusion, and Jellyfin read permissions. TV episodes and movies were confirmed in the media library.
- Ebook validation pipeline stages EPUB/PDF only after malware scanning, structural checks and integrity verification. An independent approval script revalidates before sending files to the ebook library.
- An unprivileged automatic ebook-approval worker and one-minute systemd timer were deployed. Logs confirmed successful runs and idempotent skipping of previously approved files; several books were confirmed in the ebook library. A fresh end-to-end unattended ebook download/import remains to be tested.
- Troubleshooting included repairing TV filename matching, handling movie release tags, and archiving a stale completion notice without deleting torrent data.

## Remaining work
- Complete music acquisition/organization and verify Navidrome playback.
- Configure Audiobookshelf podcast subscriptions and audiobook acquisition/playback.
- Verify unattended fresh-download ebook import, metadata retry behavior, reboot recovery, storage migration and restore procedures.

**Architecture rationale:** keep download egress isolated from private remote access, preserve seeding source files, require validation before library import, and make normal content requests hands-off. Security tests should support reliable media service, not displace the primary goal.

This public summary intentionally excludes local addresses, private paths, usernames, provider configurations, credentials and torrent identifiers.
