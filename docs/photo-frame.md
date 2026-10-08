# Integrated Digital Photo Frame

The Pi-cloud includes a digital photo frame physically integrated into the case through its built-in 4.3-inch touchscreen/display.

## Software

- labwc
- swayimg
- Python
- Bash
- Immich API
- systemd timer

## Data flow

Immich album
-> synchronization script
-> local frame directory
-> slideshow manager
-> swayimg
-> integrated case display

The Immich album is authoritative. New album photos are downloaded to the frame directory. Photos removed from the album are removed only from the frame's local copy.

The slideshow manager detects changes, restarts swayimg when needed, and restores the slideshow after reboot.

## Troubleshooting lesson

The first implementation attempted to manage the Wayland slideshow directly from the systemd sync service. A more reliable design separated responsibilities: systemd handles synchronization, while the graphical session owns the slideshow process.

Process detection was also corrected so the manager can reliably determine whether swayimg is running.
