# Lenovo IdeaPad S540 OpenCore EFI (macOS Tahoe 26.x)

<img width="1344" height="756" alt="Snímek obrazovky 2026-11-22 v 20 23 33" src="https://github.com/user-attachments/assets/d8b4a400-fd95-406d-af3e-b06e6e1a40bb" />

![macOS](https://img.shields.io/badge/macOS-Tahoe%2026.x-black?style=for-the-badge&logo=apple)
![OpenCore](https://img.shields.io/badge/OpenCore-1.0.8-blue?style=for-the-badge)
![Release](https://img.shields.io/badge/Release-v3.0-purple?style=for-the-badge)
![Image](https://img.shields.io/badge/Installer-Olarila%20Vanilla%20Tahoe-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Stable-brightgreen?style=for-the-badge)

Custom **OpenCore 1.0.8** EFI configuration tailored for the **Lenovo IdeaPad S540** (Whiskey Lake / Comet Lake iGPU `00009B3E`, iGPU-only without NVIDIA dGPU), installed using the **Olarila Vanilla macOS Tahoe** image. Tuned for responsive CPU performance (`CPUFriend`), clean S3 sleep/wake, custom 9-port USB mapping (`UTBMap`), **OpenCanopy** GUI (`Flavours-NewAppleDisk`), and **OCLP-Mod** Intel Wi-Fi & Audio patching.

---

## 📋 Changelog & Updates (`v3.0`)

- **Critical Sleep / Wake ACPI Fix:** Disabled broken orphan ACPI renames (`_PTS → ZPTS`, `_WAK → ZWAK`, `GPRW → XPRW`) that had no companion SSDTs and prevented native ACPI sleep/wake routines from executing, fixing post-sleep CPU/power degradation.
- **Activated `SSDT-XOSI.aml` (`_OSI → XOSI`):** Added the missing `_OSI → XOSI` ACPI rename patch so Windows ACPI mode, `GPI0` interrupts for the I2C precision trackpad, and Lenovo EC power/thermal paths initialize properly.
- **More Responsive CPU Power Management (`CPUFriend`):** Retuned `CPUFriendDataProvider.kext` across both frequency vectors to **`EPP 0x20`** (from `0x40`) and **`PerfBias 0x02`** (from `0x05`) while keeping `LFM 800 MHz` and `HWP` enabled for snappier Turbo Boost ramp-up.
- **Custom 9-Port USB Map (`UTBMap.kext`):** Replaced generic `UTBDefault.kext` with a custom **`USBToolBox` (`UTBMap.kext`)** mapping all **9 ports** (`2× USB-A`, `1× USB-C`, and `3× Internal` for Bluetooth/camera/reader) and disabled unnecessary `XHCI-unsupported.kext`.
- **Cleaned Up Unused ACPI & Kexts:** Disabled `SSDT-NDGP_OFF-Air14IML.aml` (not needed on iGPU-only S540 models without NVIDIA dGPU), disabled desktop-only `SMCSuperIO.kext`, and set `Misc → Debug → Target = 0`.
- **OpenCanopy GUI Theme (`Flavours-NewAppleDisk`):** Enabled `OpenCanopy.efi` (`PickerMode = External`, `PickerAttributes = 145` for Flavour icons) with the `chris1111\Flavours-NewAppleDisk` theme (includes macOS Tahoe `Apple26` & Windows 11 icons).
- **OCLP-Mod Ready:** Preserved full `IOSkywalkFamily` downgrade stack (`Kernel → Block`, `IOSkywalkFamily`, `IO80211FamilyLegacy`, `AirPortBrcmNIC`, `AMFIPass`, `AirportItlwm`), `AppleALC` (`layout-id = 15`), `SecureBootModel = Disabled`, and `csr-active-config = 030A0000`.

---

## 💻 Hardware Specifications

| Component | Specification | Status |
| :--- | :--- | :--- |
| **Model** | Lenovo IdeaPad S540 (iGPU-only, no NVIDIA dGPU) | ✅ Supported |
| **CPU** | Intel Core i3 / i5 / i7 (Whiskey Lake 8th Gen / Comet Lake 10th Gen) | ✅ Full XCPM + CPUFriend (`EPP 0x20`) |
| **iGPU** | Intel UHD Graphics 620 (`00009B3E`, `2048 MB` unified VRAM, HDMI LSPCON) | ✅ Full Metal Acceleration |
| **Audio** | Realtek ALC (`AppleALC.kext`, `layout-id = 15` + OCLP-Mod Sound Fix) | ✅ Working |
| **Wi-Fi & Bluetooth** | Intel Wireless (`OCLP-Mod` + `AirportItlwm` + `IntelBluetoothFirmware`) | ✅ Working |
| **USB Ports** | Custom 9-port `UTBMap.kext` (2× USB-A, 1× USB-C, 3× Internal) | ✅ Fully Mapped |
| **Trackpad** | I2C Precision Touchpad (`VoodooI2C` + `VoodooI2CHID` + `GPI0`) | ✅ Native MT2 Gestures |
| **Keyboard** | PS/2 Internal Keyboard (`VoodooPS2Controller` + `BrightnessKeys`) | ✅ Working |
| **Sleep / Wake** | Native ACPI `_PTS`/`_WAK` restored, `HibernateMode = None` | ✅ Working |
| **Boot Picker** | OpenCanopy GUI (`chris1111\Flavours-NewAppleDisk`) | ✅ Working |
| **SMBIOS** | `MacBookPro16,2` | ✅ Native Tahoe Support |

---

## 📂 EFI Structure (`ACPI`, `Drivers` & `Kexts`)

### ACPI (`EFI/OC/ACPI/`)
- `SSDT-PLUG.aml` — Enables native Intel `X86PlatformPlugin` (XCPM) CPU power management
- `SSDT-EC.aml` & `SSDT-USBX.aml` — Embedded Controller fix & USB power properties
- `SSDT-XOSI.aml` — Windows `_OSI` spoofing (paired with `_OSI → XOSI` patch)
- `SSDT-GPI0.aml` — Enables GPIO controller for `VoodooI2C` trackpad interrupts
- `SSDT-TPAD-Air14IML.aml` & `SSDT-RMCF-Air14IML.aml` — I2C precision trackpad (`TPXX`) configuration
- `SSDT-PNLF.aml` — Native macOS backlight control (paired with `PNLF → XNLF`)
- `SSDT-RTCAWAC.aml` — System clock compatibility patch
- `SSDT-PMC.aml` — Native NVRAM support
- `SSDT-ALS0.aml` — Ambient Light Sensor device
- `SSDT-MCHC.aml` & `SSDT-SBUS.aml` — Memory Controller & SMBus support
- *(Disabled: `SSDT-NDGP_OFF-Air14IML.aml` — only needed if your model has a discrete NVIDIA GPU)*

### UEFI Drivers (`EFI/OC/Drivers/`)
- `HfsPlus.efi` — HFS+ filesystem support
- `OpenRuntime.efi` — OpenCore runtime services
- `OpenCanopy.efi` — Graphical boot picker (`chris1111\Flavours-NewAppleDisk`, `PickerAttributes = 145`)
- `ResetNvramEntry.efi` — Reset NVRAM option in boot picker

### Kexts (`EFI/OC/Kexts/`)
- `Lilu.kext` (`v1.7.3`) — Core patching engine
- `VirtualSMC.kext` (`v1.3.8`) + `SMCProcessor.kext`, `SMCBatteryManager.kext`, `SMCLightSensor.kext` — SMC emulation & laptop sensors *(desktop-only `SMCSuperIO.kext` disabled)*
- `YogaSMC.kext` (`v1.5.3`) — Lenovo EC integration, Fn keys & thermal/battery sensors
- `CPUFriend.kext` (`v1.3.0`) + `CPUFriendDataProvider.kext` — Custom power profile (`EPP 0x20`, `PerfBias 0x02`, `LFM 800 MHz`)
- `WhateverGreen.kext` (`v1.7.1`) — iGPU framebuffer (`00009B3E`), HDMI LSPCON & Metal acceleration
- `AppleALC.kext` (`v1.9.8`) — Onboard audio (`layout-id = 15` via `DeviceProperties`)
- `BrightnessKeys.kext` (`v1.0.4`) — Native brightness hotkeys
- `NVMeFix.kext` (`v1.1.4`) — Improved NVMe SSD power management
- `USBToolBox.kext` (`v1.2.0`) + `UTBMap.kext` — Custom 9-port USB map (2× USB-A, 1× USB-C, 3× Internal)
- `VoodooPS2Controller.kext` (`v2.3.7`, keyboard plugin only) — Internal keyboard
- `VoodooI2C.kext` (`v2.9.1`) + `VoodooI2CHID.kext` — I2C Precision Touchpad & multi-touch gestures
- **OCLP-Mod Wireless Stack:** `IOSkywalkFamily.kext` (+ `Kernel → Block`), `IO80211FamilyLegacy.kext` (+ `AirPortBrcmNIC.kext`), `AMFIPass.kext` (`v1.4.1`), `AirportItlwm.kext` (`v2.3.0`), `IntelBluetoothFirmware.kext` (`v2.5.0`), `IntelBTPatcher.kext` (`v2.5.0`), `BlueToolFixup.kext` (`v2.7.3`)

---

## 🚀 USB Creation & Installation Guide (Windows)

### 1. Download & Flash the RAW Image
1. Download the **Olarila Vanilla RAW Image (macOS Tahoe)**. Here: https://www.mediafire.com/file_premium/zni17vl0io4k7t1/Olarila_Tahoe_26.7.1.raw/file?action=check
2. Open **balenaEtcher**, select the downloaded `.raw` image, choose your USB flash drive, and click **Flash!**.

### 2. Partition Setup (MiniTool Partition Wizard)
1. Open **MiniTool Partition Wizard** on Windows and locate your flashed USB drive.
2. Right-click the small **EFI** partition on the USB drive, select **Change Letter**, and assign it a drive letter (e.g., **`F:`**).
3. Select the remaining unallocated space on the USB drive, click **Create**, format it as **exFAT** or **NTFS**, and label it **`OpenCore`**.
4. Click **Apply** in the top-left corner to save the partition changes.

### 3. Copy Custom EFI & OCLP-Mod Tools
1. Download **Explorer++**, right-click it, and select **Run as administrator**.
2. Open your newly assigned EFI drive (**`F:\`**), delete the existing `EFI` folder if present, and paste the **`EFI`** folder from the latest Release into **`F:\`**.
3. Open `F:\EFI\OC\config.plist` in **ProperTree** or **OpenCore Auxiliary Tools (OCAT)** and generate unique SMBIOS values under `PlatformInfo -> Generic` for **`MacBookPro16,2`** (`SystemSerialNumber`, `MLB`, `SystemUUID`, `ROM`).
4. Open the **`OpenCore`** partition you created on the USB drive and copy the **OCLP-Mod** installer/app inside it so you have it ready offline after macOS installs.
5. On first boot in OpenCore, press **`Spacebar`** (if hidden) and select **Reset NVRAM** before booting the installer.

---

## 🛠️ Post-Install Setup (Wi-Fi, Audio, Sleep & UI Tuning)

### 1. Patch Intel Wi-Fi & Audio via OCLP-Mod
1. Boot from the USB drive, install **macOS Tahoe** onto your SSD, and complete initial setup.
2. Copy **OCLP-Mod** from your USB **`OpenCore`** partition to `/Applications` and launch it.
3. Run **Post-Install Root Patch** and apply the **Intel Wi-Fi** patch first, then **reboot**.
4. After rebooting, connect to Wi-Fi, open **OCLP-Mod** again, download support files, apply the **Sound Fix** patch, and **reboot**.

### 2. Stabilize S3 Sleep (`pmset`)
Run once in Terminal to disable macOS hibernation / DarkWake modes that cause sleep/wake issues on Hackintosh laptops (normal lid-close S3 sleep stays fully functional):

```bash
sudo pmset -a standby 0 autopoweroff 0 powernap 0 proximitywake 0 tcpkeepalive 0
```

### 3. Recommended UI & Trackpad Tweaks (Intel UHD 620)
- **Smoother UI on Tahoe:** Enable **System Settings → Accessibility → Display → Reduce transparency** to cut heavy Liquid Glass blur overhead on UHD 620.
- **Trackpad Feel:** Raise **System Settings → Trackpad → Tracking speed** to ~75–90%, or use **[LinearMouse](https://linearmouse.app/)** if you prefer a Windows Precision-like acceleration curve.

---

## 🆘 Recovery Troubleshooting (If Boot Hangs After Patching)

1. **First step — Reset NVRAM:** Reboot into the OpenCore boot picker, select **Reset NVRAM** (press `Spacebar` if hidden), and boot macOS again.
2. **If a root patch still causes a boot loop:** Boot into **macOS Recovery**, open **Utilities → Terminal**, and revert to the last sealed Apple APFS snapshot:

```bash
mount -uw "/Volumes/Macintosh HD"
bless --mount "/Volumes/Macintosh HD" --bootefi --last-sealed-snapshot
reboot
```
