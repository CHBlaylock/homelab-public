# Pi-cloud

The personal-cloud appliance is a Raspberry Pi 5 with 2 TB (1.9 TB) NVMe storage.

It provides:

- Immich photo/video library
- Nextcloud file cloud
- Private Tailscale access
- Integrated digital photo frame

The system uses Debian/Raspberry Pi OS, Docker, databases, supporting cache services, and systemd automation.

The architecture is designed to be reproducible on a second independent Pi-cloud node.

## Operating system decision

The Pi-cloud was initially installed with **Raspberry Pi OS Lite**. During development, the integrated 4.3-inch display required native display-orientation support that the Lite installation could not provide in the required form.

Before changing the operating system, backups of the working configuration/data were created. The OS was then changed to a Raspberry Pi OS installation with the graphical/display capabilities needed by the integrated digital photo frame.

This illustrates an important infrastructure practice: when an initial platform choice does not meet a hardware requirement, preserve the working state first, then change the platform and validate the resulting system.
