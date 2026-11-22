# Lenovo IdeaPad S540 OpenCore EFI (macOS Tahoe 26.x)

<img width="1344" height="756" alt="Snímek obrazovky 2026-11-22 v 20 23 33" src="https://github.com/user-attachments/assets/d8b4a400-fd95-406d-af3e-b06e6e1a40bb" />

![macOS](https://img.shields.io/badge/macOS-Tahoe%2026.x-black?style=for-the-badge&logo=apple)
![OpenCore](https://img.shields.io/badge/OpenCore-1.0.8-blue?style=for-the-badge)
![Image](https://img.shields.io/badge/Installer-Olarila%20Vanilla%20Tahoe-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Stable-brightgreen?style=for-the-badge)

Custom **OpenCore 1.0.8** EFI configuration tailored for the **Lenovo IdeaPad S540** (Whiskey Lake / Ice Lake iGPU `00009B3E`), installed using the **Olarila Vanilla macOS Tahoe** image and tuned with custom CPU power management (`CPUFriend`), updated ACPI `SSDT` patches, and **OCLP-Mod** for Intel Wi-Fi.

---

## 📋 Changelog & Updates (Tahoe Release)

- **Clean Tahoe Installation:** Installed using the **Olarila Vanilla macOS Tahoe** image paired with this custom OpenCore 1.0.8 EFI (without using the generic prebuilt Olarila EFI).
- **SMBIOS Migration:** Migrated SMBIOS to `MacBookPro16,2` for native macOS Tahoe compatibility without requiring `revpatch=sbvmm`.
- **OpenCore Updated:** Upgraded bootloader and core Acidanthera kexts to **OpenCore 1.0.8**.
- **CPU Power Management (`CPUFriend`):** Added `CPUFriend.kext` + custom `CPUFriendDataProvider.kext` (EPP `0x40` Balanced Performance, PerfBias `0x05`, LFM `800 MHz`) to match Windows 11 responsiveness and Turbo Boost behavior.
- **Updated ACPI / SSDT Tables:** Rebuilt and cleaned up `SSDT` patches (`SSDT-PLUG`, `SSDT-EC-USBX`, `SSDT-AWAC`, `SSDT-GPI0`, `SSDT-PNLF`) for clean XCPM initialization, I2C trackpad interrupts, and backlight control.
- **Intel Wi-Fi & Sound Fix:** Patched Intel Wireless via **OCLP-Mod** and resolved onboard audio cleanly via native `AppleALC.kext` (`alcid=11` / `alcid=21`) to avoid `AppleHDA` boot loops.

---

## 💻 Hardware Specifications

| Component | Specification | Status |
| :--- | :--- | :--- |
| **Model** | Lenovo IdeaPad S540 | ✅ Supported |
| **CPU** | Intel Core i3 / i5 / i7 (Whiskey Lake / Ice Lake) | ✅ Full XCPM + CPUFriend |
| **iGPU** | Intel UHD Graphics (`00009B3E` / `1536–2048 MB` VRAM) | ✅ Full Metal Acceleration |
| **Audio** | Realtek ALC257 / ALC236 (`AppleALC.kext` sound fix) | ✅ Working |
| **Wi-Fi & Bluetooth** | Intel Wireless (`OCLP-Mod` + `IntelBluetoothFirmware`) | ✅ Working |
| **Trackpad** | I2C Precision Touchpad (`VoodooI2C` + `VoodooI2CHID`) | ✅ Gestures & Polling/Interrupt |
| **Keyboard** | PS/2 Internal Keyboard (`VoodooPS2Controller`) | ✅ Working |
| **Sleep / Wake** | Hibernation disabled, S3/Modern Standby patched | ✅ Working |
| **SMBIOS** | `MacBookPro16,2` | ✅ Native Tahoe Support |

---

## 📂 EFI Structure (`ACPI` & `Kexts`)

### ACPI (`EFI/OC/ACPI/`)
- `SSDT-AWAC.aml` — System clock compatibility patch
- `SSDT-EC-USBX.aml` — Embedded Controller & USB power injection
- `SSDT-GPI0.aml` — Enables GPIO controller for smooth `VoodooI2C` trackpad operation
- `SSDT-PLUG.aml` — Enables native Intel `X86PlatformPlugin` CPU power management
- `SSDT-PNLF.aml` — Native macOS brightness slider & backlight control

### Kexts (`EFI/OC/Kexts/`)
- `Lilu.kext` — Core patching engine
- `VirtualSMC.kext` (+ `SMCProcessor.kext`, `SMCBatteryManager.kext`, `SMCSuperIO.kext`) — SMC emulation & sensors
- `CPUFriend.kext` — Dynamic CPU power management injector
- `CPUFriendDataProvider.kext` — Custom frequency & EPP (`0x40`) profile for Lenovo S540
- `WhateverGreen.kext` — iGPU framebuffer & Metal acceleration patches
- `AppleALC.kext` — Native audio codec sound fix (`alcid=11` or `alcid=21` in `boot-args`)
- `VoodooPS2Controller.kext` — Internal keyboard support
- `VoodooI2C.kext` + `VoodooI2CHID.kext` — Precision trackpad & multi-touch gestures
- `IntelBluetoothFirmware.kext` + `IntelBTPatcher.kext` + `BlueToolFixup.kext` — Bluetooth stack for Tahoe
- `NVMeFix.kext` — Improved NVMe SSD power management & thermal efficiency
- `USBMap.kext` — Custom USB port mapping for `MacBookPro16,2`

---

## 🚀 USB Creation & Installation Guide (Windows)

### 1. Download & Flash the RAW Image
1. Download the **Olarila Vanilla RAW Image (macOS Tahoe)**.
2. Open **balenaEtcher**, select the downloaded `.raw` image, choose your USB flash drive, and click **Flash!**.

### 2. Partition Setup (MiniTool Partition Wizard)
1. Open **MiniTool Partition Wizard** on Windows and locate your flashed USB drive.
2. Right-click the small **EFI** partition on the USB drive, select **Change Letter**, and assign it a drive letter (e.g., **`F:`**).
3. Select the remaining unallocated space on the USB drive, click **Create**, format it as **exFAT** or **NTFS**, and label it **`OpenCore`**.
4. Click **Apply** in the top-left corner to save the partition changes.

### 3. Copy Custom EFI & OCLP-Mod Tools
1. Download **Explorer++**, right-click it, and select **Run as administrator**.
2. Open your newly assigned EFI drive (**`F:\`**), delete the existing `EFI` folder if present, and paste the **`EFI`** folder from this repository into **`F:\`**.
3. Open `F:\EFI\OC\config.plist` in **ProperTree** or **OpenCore Auxiliary Tools (OCAT)** and generate unique SMBIOS values under `PlatformInfo -> Generic` for **`MacBookPro16,2`** (`SystemSerialNumber`, `MLB`, `SystemUUID`, `ROM`).
4. Open the **`OpenCore`** partition you created on the USB drive and copy the **OCLP-Mod** installer/app inside it so you have it ready offline after macOS installs.

---

## 🛠️ Post-Install Setup (Wi-Fi, Audio & Updates)

### Patch Intel Wi-Fi & Audio via OCLP-Mod
1. Boot from the USB drive, install **macOS Tahoe** onto your SSD, and complete the initial setup.
2. Open the **`OpenCore`** partition on your USB drive, copy **OCLP-Mod** to `/Applications`, and launch it.
3. Run **Post-Install Root Patch** and apply the **Intel Wi-Fi** patch first, then **reboot**.
4. After rebooting, connect to your Wi-Fi network, open **OCLP-Mod** again, download the required support files, and apply the **Sound Fix** patch, then **reboot**.

---

## 🆘 Recovery Troubleshooting (If Boot Hangs After Patching)

1. **First step — Reset NVRAM:** Reboot into the OpenCore boot picker, select **Reset NVRAM** (press `Spacebar` if hidden), and boot macOS again.
2. **If a root patch still causes a boot loop:** Boot into **macOS Recovery**, open **Utilities → Terminal**, and revert to the last sealed Apple APFS snapshot:

```bash
mount -uw "/Volumes/Macintosh HD"
bless --mount "/Volumes/Macintosh HD" --bootefi --last-sealed-snapshot
reboot
