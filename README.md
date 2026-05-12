# Creality-K1C-LiDAR-Installer
One-click installer for enabling Creality K1C LiDAR support with Guilouz Helper Script, Moonraker, Fluidd, KAMP and custom calibration patches.

**K1C LiDAR Installer Script by Andrea Pezzino**  
Version: **1.0.0**

Automated software patch and installer for enabling the Creality LiDAR workflow on the **Creality K1C** using firmware **1.3.2.31**, Guilouz Helper Script, Moonraker, Fluidd, KAMP and custom LiDAR/Pressure Advance patches.

---

## Demo

YouTube video showing the full print start workflow through LiDAR:

[YOUTUBE VIDEO PLACEHOLDER]

---

## Hardware Mod

### LiDAR Retrofit

To install the LiDAR sensor on a Creality K1 / K1C, you need the following components:

- Creality K1 LiDAR  
  https://it.aliexpress.com/item/1005007285806062.html

- USB-J55 cable  
  https://it.aliexpress.com/item/1005010246791988.html

You also need to print the required mounting parts:

- Creality K1 Max AI LiDAR mounting plate  
  https://www.printables.com/model/773067-creality-k1-max-ai-lidar-mounting-plate

- LiDAR mount / adapter  
  https://www.thingiverse.com/thing:6895960

And the calibration sticker:

- Creality K1 Max LiDAR calibration sticker  
  https://www.printables.com/model/1056491-creality-k1-max-lidar-callibration-sticker/files

The sticker must be printed and attached to the sticker holder, which is then screwed onto the build plate.

The sticker should be placed roughly in the center of the holder. It does not need to be positioned with perfect accuracy, because the firmware will try to locate it automatically during calibration.

This video explains the physical installation process on the K1 / K1C:

https://www.youtube.com/watch?v=Hg21z1GTKGg

---

## Firmware Requirement

Creality disabled K1/K1C LiDAR compatibility in firmware versions **1.3.3+**, making the LiDAR workflow officially available only on the K1 Max.

The good news is that there is one K1C firmware version that is fully compatible with the LiDAR workflow:

**1.3.2.31**

You can download the required firmware here:

https://www.crealitycloud.cn/en/downloads/firmware/flagship-series/k1c

You must downgrade or install firmware **1.3.2.31** before using this patch.

This is not a major drawback: later firmware versions do not appear to bring any meaningful improvement to print quality, and with Moonraker + Fluidd installed, the K1C becomes much more flexible and powerful.

The installer will check the firmware version before applying the patch. If the detected firmware is not exactly **1.3.2.31**, the installation will stop.

---

## Root Access Requirement

Before running the software patch, you need to enable root access on the printer.

Once root access is enabled, connect to the printer through SSH:

```sh
ssh root@YOUR_PRINTER_IP
```

Replace `YOUR_PRINTER_IP` with the actual IP address of your K1C.

> Password is creality_2023.

---

## Software Patch Installation

Download the installer directly on the printer using `wget`:

```sh
cd /usr/data
wget https://raw.githubusercontent.com/sibest/Creality-K1C-LiDAR-Installer/main/k1c-install-lidar.sh
chmod +x k1c-install-lidar.sh
sh k1c-install-lidar.sh
```

After the installation completes:

```txt
Power the printer OFF, wait a few seconds, then power it ON again.
```

A full power cycle is required after installation.

---

## What the Installer Does

The installer performs the following steps automatically:

1. Checks that the printer firmware is exactly **1.3.2.31**

2. Checks if Guilouz Helper Script is already installed at:

   ```txt
   /usr/data/helper-script/helper.sh
   ```

3. If an existing Helper Script installation is found:
   - removes Fluidd through the existing Helper Script
   - removes Moonraker and Nginx through the existing Helper Script
   - removes the old Helper Script folder
   - performs cleanup of known old web stack paths

4. Downloads and installs Guilouz Creality Helper Script **6.2.0**:

   ```txt
   https://github.com/Guilouz/Creality-Helper-Script/archive/refs/tags/6.2.0.zip
   ```

5. Automatically installs the following Guilouz Helper Script modules:
   - Moonraker and Nginx
   - Fluidd, on port **4408**
   - Klipper Adaptive Meshing & Purging
   - Nozzle Cleaning Fan Control
   - Fans Control Macros
   - Improved Shapers Calibrations
   - Useful Macros
   - Save Z-Offset Macros
   - Screws Tilt Adjust Support
   - M600 Support

6. Verifies that the required Helper Script modules were installed

7. Applies the custom K1C LiDAR patch files

8. Adds the required includes to `printer.cfg`

9. Asks the user to power the printer off and back on

---

## Manual Guilouz Helper Script Installation

The installer can perform this automatically, but if you want to do it manually, follow Guilouz’s official guide:

https://guilouz.github.io/Creality-Helper-Script-Wiki/helper-script/helper-script-installation/

Download Guilouz Helper Script version **6.2.0** from:

https://github.com/Guilouz/Creality-Helper-Script/releases/tag/6.2.0

Run the Helper Script with:

```sh
sh /usr/data/helper-script/helper.sh
```

Then install the following menu entries:

```txt
1)  Moonraker and Nginx
2)  Fluidd (port 4408)
6)  Klipper Adaptive Meshing & Purging
8)  Nozzle Cleaning Fan Control
9)  Fans Control Macros
10) Improved Shapers Calibrations
11) Useful Macros
12) Save Z-Offset Macros
13) Screws Tilt Adjust Support
14) M600 Support
```

---

## Included Patch Files

The LiDAR patch includes the following files:

```txt
/usr/data/printer_data/config/apply_lidar_pa.py
/usr/data/printer_data/config/gcode_macro.cfg
/usr/data/printer_data/config/Helper-Script/KAMP/Start_Print.cfg
/usr/share/klipper/klippy/extras/custom_macro.py
/usr/data/moonraker/moonraker/moonraker/components/klippy_apis.py
/usr/data/printer_data/config/k1c_calibration_toggle.cfg
/usr/data/printer_data/config/lidar_macros.cfg
/usr/data/k1c_calibration_toggle.sh
/usr/data/k1c_creality_header_injector.py
/usr/share/klipper/klippy/extras/prtouch.py
/usr/share/klipper/klippy/extras/prtouch_v2.py
/usr/share/klipper/klippy/extras/prtouch_v1_wrapper.cpython-38-mipsel-linux-gnu.so
/usr/share/klipper/klippy/extras/prtouch_v2_wrapper.cpython-38-mipsel-linux-gnu.so
```

`printer.cfg` is **not** included in the payload.

The installer only edits `printer.cfg` to add these includes if they are missing:

```cfg
[include k1c_calibration_toggle.cfg]
[include lidar_macros.cfg]
```

---

## Resulting Print Start Workflow

After applying this patch, the print start workflow becomes:

1. Full **9x9 bed mesh** calculation, used to improve the accuracy of the LiDAR calibration test lines
2. Adaptive bed mesh calculation for the actual printed objects
3. LiDAR calibration
4. Pressure Advance calibration line print
5. LiDAR scan of the calibration lines
6. Automatic Pressure Advance calculation
7. Adaptive purge line
8. First layer print
9. LiDAR first layer defect scan
10. Second adaptive purge line
11. Print resume

---

## Patch Details

### START_PRINT / KAMP Patch

One of the most important parts of this project is the modification of the print start flow to make the following systems work together:

- KAMP / Adaptive Purge
- Creality LiDAR workflow
- Fluidd / Moonraker

The main problem is that the Creality print workflow and the standard Klipper/Moonraker workflow are completely different.

For this reason, the KAMP `START_PRINT` flow was modified by adding a dynamic switch that selects the correct print start method:

- normal mode ? standard Fluidd/Moonraker print start
- calibration mode ? Creality LiDAR/flow calibration workflow

This allows the system to decide whether to use the normal Klipper start sequence or call the local Creality service required for automatic LiDAR calibration.

This avoids having to maintain two separate printer configurations.

---

### Print Trigger Through the Local Creality Service

Moonraker normally starts a print directly through Klipper.

The problem is that the LiDAR workflow is not actually used if the G-code file is launched only through the normal Moonraker/Klipper path.

For this reason, the print start flow was patched mainly inside:

```txt
/usr/data/moonraker/moonraker/moonraker/components/klippy_apis.py
```

The patch intercepts the print start request and calls the local Creality service through a websocket on localhost:

```txt
127.0.0.1
```

instead of using the normal Moonraker-only print start path.

This is critical because the Creality firmware activates the LiDAR calibration workflow only through the Creality print service.

Without this patch, the LiDAR sensor may be physically installed and detected, but it cannot be properly used when printing from Orca Slicer or by uploading G-code through Fluidd.

---

### Creality Header Injector

One major issue was that LiDAR/flow calibration would not start unless the G-code looked like an “official” Creality-generated file.

To solve this, the patch includes an automatic G-code header injector.

It checks uploaded G-code files and reinserts the required Creality metadata headers.

Example:

```gcode
;FLAVOR:Creality OS
;Machine Name:K1C
;Material Type:ABS
;Creality Print GCode Generated by CXEngine
```

This fixes errors such as:

```txt
current file does not support flow detection
```

In practice, the LiDAR sensor existed and the firmware feature existed, but the Creality system refused to use the LiDAR workflow unless the file had the expected Creality signature.

The injection is performed automatically on uploaded files, so it is no longer necessary to use Creality Print exclusively.

---

### Automatic Pressure Advance Reading from Creality Logs

Another important issue is that Creality performs the Pressure Advance calculation, but does not reliably apply the calculated value at print start.

To fix this, the patch includes a script that reads the Creality AI middleware log directly:

```txt
/usr/data/creality/userdata/log/cx_ai_middleware.log
```

The script searches for the latest line containing:

```txt
[GetFlowDetectNewFromAi]result=
```

and uses that value to automatically set the correct Pressure Advance.

This patch is required because, even when the firmware correctly calculates the Pressure Advance value through the LiDAR test, it may still apply a default value such as `0.05`.

---

### First Floor Resume and Second Purge Line Patch

The `first_floor_pause` and `first_floor_resume` macros were also modified.

After the first layer is printed, the Creality firmware pauses the print and scans the first layer with the LiDAR sensor to check for defects.

During this pause, a small amount of material can build up on the nozzle. When the print resumes, this can leave unwanted material on the printed part.

To reduce this issue, the patch performs a short second purge line before resuming the print.

---

### Updated Bed Leveling API Files

The patch package also includes the updated bed leveling API files taken from the official Creality firmware version **1.3.3.26**.

These files are included to improve compatibility while still keeping the printer on firmware **1.3.2.31**, which is required for the K1C LiDAR workflow.

---

## Fluidd Access

After installation and reboot, Fluidd should be available on port:

```txt
4408
```

Open:

```txt
http://YOUR_PRINTER_IP:4408
```

---

## Installer Options

Show included patch files:

```sh
sh /usr/data/k1c-install-lidar.sh --list
```

Skip firmware check:

```sh
sh /usr/data/k1c-install-lidar.sh --skip-fw-check
```

Use this only if you are absolutely sure the printer is already running firmware **1.3.2.31**.

Skip old Helper Script cleanup:

```sh
sh /usr/data/k1c-install-lidar.sh --skip-old-helper-cleanup
```

Skip Guilouz Helper Script bootstrap:

```sh
sh /usr/data/k1c-install-lidar.sh --skip-helper-bootstrap
```

Patch-only mode:

```sh
sh /usr/data/k1c-install-lidar.sh --patch-only
```

Use patch-only mode only if Guilouz Helper Script, Moonraker, Fluidd, KAMP and all required helper modules are already correctly installed.

---

## Backup and Restore

Before applying the LiDAR patch files, the installer creates a backup directory like:

```txt
/usr/data/k1c_lidar_install_backup_YYYYMMDD_HHMMSS
```

To restore the previous files:

```sh
sh /usr/data/k1c_lidar_install_backup_YYYYMMDD_HHMMSS/restore_backup.sh
```

After restoring, power the printer off and back on.

---

## Troubleshooting

### The installer says the firmware is not 1.3.2.31

Install or downgrade to firmware **1.3.2.31** using the firmware page:

https://www.crealitycloud.cn/en/downloads/firmware/flagship-series/k1c

Then power cycle the printer and run the installer again.

### Fluidd does not open after installation

Make sure you are using port **4408**:

```txt
http://YOUR_PRINTER_IP:4408
```

If it still does not open, power cycle the printer.

### Klipper reports a configuration error

Open Fluidd and check the exact error.

Common causes:

- missing include in `printer.cfg`
- duplicated macro names
- manually modified config files
- incomplete Helper Script installation
- firmware mismatch

### LiDAR calibration does not start

Make sure:

- firmware is exactly **1.3.2.31**
- the LiDAR is physically connected
- the USB-J55 cable is installed correctly
- the calibration sticker is mounted on the bed
- the G-code file was uploaded after the header injector patch was installed
- the print was started through the patched Fluidd/Moonraker workflow

---

## Disclaimer

This project is not affiliated with Creality, Guilouz, Klipper, Moonraker, Fluidd or KAMP.

Use at your own risk.