# Media server implementation: October 2026

A dedicated Proxmox Ubuntu VM hosts a Docker Compose media stack including **Jellyfin, Sonarr, Radarr, Prowlarr, Seerr, Calibre-Web Automated, Shelfmark, Navidrome, and Audiobookshelf**. Sonarr, Radarr and Prowlarr support library organization and metadata management.

## Verified engineering work
- Private remote access through Tailscale, including Jellyfin access over cellular, without router port forwarding.
- Automated movie/TV library importers using Linux services/timers, release-name parsing, sample exclusion, and Jellyfin read permissions. TV episodes and movies were confirmed in the media library.
- Ebook validation pipeline stages EPUB/PDF only after malware scanning, structural checks and integrity verification. An independent approval script revalidates before sending files to the ebook library.
- An unprivileged automatic ebook-approval worker and one-minute systemd timer were deployed. Logs confirmed successful runs and idempotent skipping of previously approved files; several books were confirmed in the ebook library. A fresh end-to-end unattended ebook download/import remains to be tested.
- Troubleshooting included repairing TV filename matching, handling movie release tags, and resolving stale processing notices while preserving source files.

## Remaining work
- Complete music acquisition/organization and verify Navidrome playback.
- Configure Audiobookshelf podcast subscriptions and audiobook acquisition/playback.
- Verify unattended fresh-download ebook import, metadata retry behavior, reboot recovery, storage migration and restore procedures.

**Architecture rationale:** separate application responsibilities, require validation before library import, preserve source files during processing, and automate routine library maintenance. Security testing should support reliable service delivery.

This public summary intentionally excludes local addresses, private paths, usernames, credentials and sensitive configuration.
