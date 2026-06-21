## Overview

- **Current OS:** macOS Sequoia
- **OpenCore Version:** 1.0.7
- **SMBIOS:** MacBookPro15,2

## Hardware Configuration

| Component | Specification | Status |
| :--- | :--- | :--- |
| **CPU** | Intel Core i3-8145U | Working |
| **GPU** | Intel UHD Graphics 620 | Working |
| **RAM** | DDR4 Dual-Channel | Working |
| **Audio** | Realtek ALC | Working |
| **Wi-Fi / BT** | Intel Wireless | Working |
| **Trackpad** | I2C Multi-touch | Working |

---

## What is Working

*   Graphics Acceleration with full Metal support
*   Audio output & Microphone input
*   Wi-Fi & Bluetooth with native handover
*   Full Trackpad gestures via I2C
*   Battery status management
*   Sleep, Wake, and Power Management

## What is Not Working

*   None so far everything is stable

---

## Setup Instructions

### 1. BIOS Settings

Ensure these values are configured before booting

*   **Disable:** Secure Boot, Intel SGX
*   **Enable:** Intel Virtualization Technology, SATA Mode: AHCI, DVMT Pre-Allocated: 64M/128M

### 2. Post-Installation

> ⚠️ **IMPORTANT:** You MUST generate your own SMBIOS data (Serial Number, UUID, MLB, ROM) using GenSMBIOS before logging into iCloud services

Enjoy your native macOS Sequoia experience on the S540
