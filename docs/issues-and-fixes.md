# Issues and Fixes

This public version records the technical lessons while omitting sensitive infrastructure details.

## LAN and Tailscale routing conflict

**Symptom:** A home Windows client could not reach local services while Tailscale was enabled.

**Diagnosis:** Competing LAN and Tailscale routes caused local traffic to use the Tailscale interface.

**Fix:**

```
tailscale set --accept-routes=false
```

**Lesson:** Use the LAN for local access and Tailscale for remote access when a device remains permanently at home.

## Nextcloud trusted-domain problem

**Symptom:** Nextcloud rejected access using the LAN address.

**Diagnosis:** The LAN endpoint was not present in Nextcloud's trusted-domain configuration.

**Fix:** Add the LAN endpoint to the trusted-domain configuration while retaining the remote Tailscale endpoint.

**Lesson:** Web applications may require explicit trusted-host configuration even when network connectivity itself is working.

## Immich mobile backup appeared stalled

**Symptom:** A large initial phone backup appeared to stop progressing.

**Resolution:** Reopening the mobile application allowed the backup to resume. Temporary upload errors subsequently completed successfully.

**Lesson:** Large mobile backups should be monitored to completion and verified before originals are removed.

## Photo-frame synchronization

**Symptom:** Photos removed from the Immich album continued appearing on the frame.

**Diagnosis:** The first synchronization logic downloaded additions but did not remove stale local copies.

**Fix:** The album became authoritative. New assets are downloaded and local copies no longer present in the album are removed.

## Slideshow process management

**Symptom:** The slideshow could disappear after an automatic refresh.

**Diagnosis:** The graphical Wayland application was being managed from the wrong service context, and process detection was unreliable.

**Fix:** File synchronization and graphical slideshow management were separated. The graphical session owns the slideshow manager, which starts and monitors swayimg.

## Reboot validation

After configuration changes and operating-system maintenance, the system was reboot-tested. The containerized services and integrated photo frame returned automatically.

## General troubleshooting approach

- Reproduce the problem.
- Inspect actual routes, processes, services, and logs.
- Change one relevant component at a time.
- Retest the failure case.
- Reboot-test important automation.
- Document the final architecture and the reason for the fix.
