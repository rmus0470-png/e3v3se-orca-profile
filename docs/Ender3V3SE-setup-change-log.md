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
- Migrated custom theme folder. Old plugin folders were initially skipped; they were subsequently copied into the new plugin folder with `cp -an`, preserving existing Moonraker plugins. The resulting folder inventory included AutoTowersGenerator, CalibrationShapesReborn, DragToClone, MeshTools, OctoPrintPlugin, SettingsGuide2, SupportFins, ThemeCreator, MoonrakerConnection, and MoonrakerPrintFollower. Several migrated plugin entries were visible in Cura's Extensions menu. Folder transfer was confirmed; full compatibility of every plugin was not established.
- Kept the new Cura 5.14 `cura.cfg` rather than replacing it wholesale, preserving its Moonraker configuration and avoiding stale plugin settings.
- Created desktop launcher at `~/.local/share/applications/ultimaker-cura-5.14.desktop`.
- Extracted Cura icon from AppImage and installed it at `~/.local/share/icons/hicolor/128x128/apps/cura-icon.png`; launcher icon set to `cura-icon`.
- Renamed the AppImage to a stable, version-independent path:
  `~/Applications/UltiMaker-Cura.AppImage`
- Updated launcher Exec path to that stable filename. Future updates should replace this AppImage while retaining the same filename and launcher path.
- Cura 5.14 was opened successfully and displayed the Creality Ender-3 V3 SE printer, Generic PETG material, 0.4 mm nozzle, and PETG Basic Standard / Standard Quality / 0.2 mm profile.
- Machine G-code flavor was confirmed set to **Marlin**. Leave this unchanged for the current Ender 3 V3 SE + Klipper setup unless there is a specific reason to revisit it.
- The Moonraker upload dialog was configured with **Start printing after upload by default** enabled. A later Cura notification explicitly confirmed that `Untitled.gcode` uploaded to the Creality Ender-3 V3 SE and the print started.

## Cura Moonraker plugins and camera integration

- Cura Marketplace showed **Moonraker Connection 1.9.1** and **Moonraker Print Follower 6.0.0** as installed.
- Cura Preferences > Printers showed plugin controls **Configure Moonraker** and **Connect Moonraker** for the existing printer.
- Moonraker connection URL configured in Cura: `http://10.0.0.224`.
- Cura's **Connect Moonraker > Monitor** tab has a separate camera URL field. Configured it to:
  `http://10.0.0.225:8081/?action=stream`
- Saved the camera URL with rotation 0° and Mirror disabled.
- **Verified visually:** Cura 5.14 Monitor displays the live camera image after saving this URL.
- Moonraker on CT 224 initially reported an empty webcam list using `GET /server/webcams/list`.
- Registered the existing external MJPG-Streamer feed through Moonraker's webcam API with:
  - Name: `Ender Camera`
  - Location: `printer`
  - Service: `mjpegstreamer`
  - Stream URL: `http://10.0.0.225:8081/?action=stream`
  - Target FPS: 15; idle FPS: 5; aspect ratio: 16:9; enabled: true.
- Moonraker returned a successful webcam object and a subsequent `GET /server/webcams/list` confirmed the saved `Ender Camera` entry.
- **Verified visually:** Mainsail dashboard displays the live camera feed, with 15 FPS shown in the camera panel.
- The working MJPG-Streamer service on CT 225 was not changed during this integration work.

## Cura preview and bed mesh clarification

- In the Cura Monitor screenshot provided on 2026-11-06, the large colored square is the bed mesh visualization. The red inset rectangle outlines the bed mesh's measured region; it is not the model's brim or skirt and is not the print-placement boundary.
- The actual printed object appears as a small item near the center of the bed in that view.
- Do not move or center the model based on the red mesh outline. Treat mesh visualization and model placement as separate things.
- The screenshot establishes what the red rectangle represents visually in that view; it does not establish any need to change bed mesh, Z offset, or printer calibration.

## Wipe/retraction investigation

- User identified a Cura setting named **Wipe Retraction Enable**. The profile default was True, and the user's current change was set to False.
- Do not assume this setting disables a separate periodic wipe movement routine. It may control wipe behavior associated with retraction; whether it removes the periodic moves must be checked in newly sliced G-code.
- The periodic left/right movement routine and the initial nozzle-cleaning motion at the left edge of the bed are separate reported behaviors. Do not conflate them.
- No confirmed source or exact G-code for the periodic routine is recorded here. Do not claim its layer schedule or movement coordinates without inspecting the actual G-code.
- Do not change global flow, Z offset, or bed mesh to address the periodic movement without evidence.

## Crowsnest status

- On CT 224, checked for Crowsnest: `command -v crowsnest` returned no executable and `systemctl status crowsnest` reported that `crowsnest.service` could not be found.
- Reopened the existing KIAUH installer at `/root/kiauh/kiauh.sh` and selected Crowsnest. KIAUH downloaded the source to `/root/crowsnest`, but installation failed because Crowsnest's installer rejects execution as root.
- CT 224 is a root-based Debian 13 container with no regular user accounts or `/home` directory. No workaround or forced installation was performed. Crowsnest is **not installed**.
- Crowsnest is not required for the current setup: the existing MJPG-Streamer stream is registered in Moonraker and displays in both Mainsail and Cura.

## Explicitly not completed / do not assume

- Do not assume Crowsnest is installed. Its source was downloaded, but its installation failed and no service was created.
- The webcam is confirmed working in both Mainsail and Cura Monitor.
- Do not replace the stable AppImage filename with a versioned filename during future Cura updates.
- Do not remove the old Cura 5.13 AppImage from Downloads until the user explicitly decides it is no longer needed.
- Do not treat every migrated Cura plugin as proven compatible merely because its folder was copied. Compatibility was only partially verified through visible menu entries.
- Do not infer that the periodic G-code wipe routine has been eliminated by changing **Wipe Retraction Enable**; verify by inspecting a fresh slice.
