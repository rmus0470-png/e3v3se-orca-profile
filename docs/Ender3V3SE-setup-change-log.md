# Ender 3 V3 SE Setup and Change Log

This log records actions actually completed in the Ender 3 V3 SE calibration/setup work. Unverified items are explicitly marked.

## Network and services

- Proxmox VE host: `10.0.0.210`.
- Klipper/Moonraker container: `10.0.0.224`.
- OctoPrint container: `10.0.0.223`, port `5000`.
- Independent webcam container: `10.0.0.225`.

## Independent webcam

- Created Debian 13 LXC container `printercam` (CT 225), configured USB camera device passthrough for `/dev/video0` and `/dev/video1`.
- Built MJPG-Streamer under `/opt/mjpg-streamer/mjpg-streamer-experimental`.
- Created and enabled systemd service `mjpg-streamer.service`.
- Confirmed service active and live stream accessible at:
  `http://10.0.0.225:8081/?action=stream`
- Working capture device: `/dev/video0`; selected stream mode: 1280x720 at 15 FPS.
- This webcam service is independent of OctoPrint and Klipper.

## Cura 5.14 on Fedora

- Existing Cura 5.13 Flatpak data was located under `~/.var/app/com.ultimaker.cura/`.
- Installed Cura 5.14.0 AppImage initially as `~/Applications/UltiMaker-Cura-5.14.0-linux-X64.AppImage`.
- Backed up new Cura data directory to `~/.local/share/cura/5.14.pre-migration`.
- Migrated custom printer/profile/material/quality data from Cura 5.13 into `~/.local/share/cura/5.14/`, including machine instances, user profiles, materials, quality profiles and related definition folders.
- Migrated custom theme folder. Old plugin folders were deliberately not copied over the new installation.
- Kept the new Cura 5.14 `cura.cfg` rather than replacing it wholesale, preserving its Moonraker configuration and avoiding stale plugin settings.
- Created desktop launcher at `~/.local/share/applications/ultimaker-cura-5.14.desktop`.
- Extracted Cura icon from AppImage and installed it at `~/.local/share/icons/hicolor/128x128/apps/cura-icon.png`; launcher icon set to `cura-icon`.
- Renamed the AppImage to a stable, version-independent path:
  `~/Applications/UltiMaker-Cura.AppImage`
- Updated launcher Exec path to that stable filename. Future updates should replace this AppImage while retaining the same filename and launcher path.
- Cura 5.14 was opened successfully and displayed the Creality Ender-3 V3 SE printer, Generic PETG material, 0.4 mm nozzle, and PETG Basic Standard / Standard Quality / 0.2 mm profile.

## Cura Moonraker plugins and camera integration status

- Cura Marketplace showed **Moonraker Connection 1.9.1** and **Moonraker Print Follower 6.0.0** as installed.
- Cura Preferences > Printers showed plugin controls **Configure Moonraker** and **Connect Moonraker** for the existing printer.
- The Moonraker host is known to be `10.0.0.224`; the exact Cura plugin connection configuration has not yet been verified or tested in this session.
- Cura Monitor displayed a camera-not-configured message.
- The independent webcam stream is confirmed working at `http://10.0.0.225:8081/?action=stream`.
- Cura camera configuration is NOT yet completed.
- **Crowsnest was not installed during this work.** Its installation status on the Klipper container has not been checked. Do not assume it is installed or absent without inspecting the Klipper container.
- No changes were made to the working MJPG-Streamer service while discussing Cura camera integration.

## Explicitly not completed / do not assume

- Do not assume Crowsnest is installed.
- Do not assume the Cura Moonraker plugin is connected successfully.
- Do not assume Cura camera integration is configured.
- Do not replace the stable AppImage filename with a versioned filename during future Cura updates.
- Do not remove the old Cura 5.13 AppImage from Downloads until the user explicitly decides it is no longer needed.
