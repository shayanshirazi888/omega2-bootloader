# ⚡ PM220x Embedded Bootloader

<div align="center">

![SoC](https://img.shields.io/badge/SoC-MT7628%20%2F%20HLK--7628N-blue?style=for-the-badge&logo=microchip)
![Arch](https://img.shields.io/badge/Architecture-MIPS%2024KEc-green?style=for-the-badge)
![Board](https://img.shields.io/badge/Hardware-PM2208%20%7C%20PM2202-orange?style=for-the-badge)
![License](https://img.shields.io/badge/License-GPL--2.0-lightgrey?style=for-the-badge)

<p align="center">
  <b>Hardened industrial U-Boot bootloader featuring dual-target builds, Web Recovery Failsafe, and hardware shift-register diagnostics for MT7628 embedded routers & cellular modems.</b>
</p>

---

</div>

## 📌 Overview

This repository contains the tailored, industrial-grade **U-Boot bootloader** optimized for **HLK-7628N / MT7628** IoT gateway and cellular router platforms. It includes customized drivers for hardware peripheral control, modular board configuration (`PM2208` & `PM2202`), dual-SIM modem power sequencing, shift-register LED indicators, and an embedded HTTP Web Recovery interface.

---

## ✨ Key Features

* **Dual Board Architecture:**
  * **PM2208:** Advanced industrial dual-SIM router with 3x cascaded `74HC595` shift registers (RGB WWAN, RS485/UART control, SIM/modem power) and hardware reset on **GPIO 17**.
  * **PM2202:** Compact IoT router variant with shift-register LED status outputs and hardware reset on **GPIO 36**.
* **Isolated Build Artifacts:** Automated compilation generates all production binaries directly into the `bin/` directory without workspace clutter.
* **Web Recovery Failsafe:** Built-in lightweight HTTP server (`uIP httpd`) accessible at `192.168.8.1` for flashing firmware or bootloader binaries in emergency unbrick mode.
* **Reliable Payload Transfer:** Hardened TCP packet handling ensuring bit-exact binary transfers for large squashfs sysupgrade images.
* **Per-Board LED Diagnostics:** Dynamic boot-time status and failsafe visual feedback via hardware shift registers.

---

## 🛠 Hardware Specifications

| Parameter | PM2208 | PM2202 |
| :--- | :--- | :--- |
| **SoC** | MediaTek MT7628 / HLK-7628N | MediaTek MT7628 / HLK-7628N |
| **CPU Core** | MIPS 24KEc @ 580 MHz | MIPS 24KEc @ 580 MHz |
| **Memory** | DDR2 16-bit bus (1024Mbit / 128MB) | DDR2 16-bit bus (1024Mbit / 128MB) |
| **Flash** | SPI NOR Flash (32MB) | SPI NOR Flash (32MB) |
| **Modem Reset Pin** | `GPIO 17` (Active-Low pulse) | `GPIO 36` (Active-Low pulse) |
| **Output Expansion** | 3x `74HC595` Shift Registers | `74HC595` Shift Register |
| **Cellular Support** | Mini PCIe LTE / Dual SIM | Mini PCIe LTE / Single SIM |
| **Default Recovery IP**| `192.168.8.1` | `192.168.8.1` |

---

## 🚀 Quick Start & Build Guide

### 1. Prerequisites & Toolchain

The build system requires the 32-bit MIPS cross-compilation toolchain:
```bash
# Verify the MIPS compiler is installed:
/opt/buildroot-gcc342/bin/mipsel-linux-gcc -v
```

If setting up the toolchain for the first time, run the environment setup script:
```bash
sh setup_env.sh
```

### 2. Compilation

To build **both** board variants in a single step:
```bash
make
```

Or compile a specific target individually:
```bash
# Build PM2208 Bootloader
make pm2208

# Build PM2202 Bootloader
make pm2202
```

### 3. Build Outputs

All production-ready images are automatically saved in the isolated `bin/` folder:

```text
bin/
├── uboot_pm2208.bin     # Flashable bootloader for PM2208 (GPIO 17 reset)
├── uboot_pm2202.bin     # Flashable bootloader for PM2202 (GPIO 36 reset)
└── uboot.bin            # Default bootloader image (points to PM2208)
```

To clean intermediate object files:
```bash
make clean
```

To completely wipe all generated binaries and the `bin/` directory:
```bash
make distclean
```

---

## 🌐 Web Recovery Mode (Failsafe)

When the router cannot boot OpenWrt or requires an unbrick procedure, the built-in HTTP Web Server enables browser-based flashing.

```
       [ PC / Laptop ] 
       IP: 192.168.8.8
             │
      (Ethernet Cable)
             │
             ▼
     [ PM220x Router ]
      IP: 192.168.8.1  (Web Failsafe Server)
```

### How to Enter Web Recovery:
1. Connect an Ethernet cable directly between your computer and the router's LAN port.
2. Configure your computer's network interface to static IP:
   * **IP Address:** `192.168.8.8` (or any `192.168.8.x` except `.1`)
   * **Subnet Mask:** `255.255.255.0`
   * **Gateway:** `192.168.8.1`
3. Power off the board.
4. Press and hold the **Reset Button**, then power on the device.
5. Keep holding the button for **~5 to 8 seconds** until the diagnostic LEDs blink rapidly into recovery pattern.
6. Open your web browser and navigate to:
   ```text
   http://192.168.8.1
   ```
7. Select your OpenWrt `sysupgrade.bin` firmware and click **Upload Firmware**. The device will write to SPI Flash and automatically reboot.

---

## 📟 Serial UART Console

For development, debugging, and boot log inspection:

* **Baud Rate:** `115200`
* **Data Bits:** `8`
* **Parity:** `None`
* **Stop Bits:** `1`
* **Flow Control:** `None`

---

## 📂 Project Structure

```text
├── bin/                  # Generated binary outputs (uboot.bin, uboot_pm*.bin)
├── board/                # Board initialization and board-specific memory mapping
├── common/               # Core U-Boot shell commands, memory, and environment
├── cpu/ralink_soc/       # MT7628/HLK-7628N SoC low-level CPU & clock code
├── drivers/              # Hardware peripheral drivers (Ethernet, SPI, Flash)
├── httpd/                # Web Recovery HTTP server and upload daemon
├── include/              # Header configurations (rt2880.h, etc.)
├── lib_mips/             # MIPS architecture initialization and board.c
├── Makefile              # Modular build system with dual-target generation
└── README.md             # Project documentation
```

## 🔍 Keywords & Topics

```text
Keywords: MT7628, HLK-7628N, OpenWrt, U-Boot, Bootloader, Web Recovery, Failsafe, Dual-SIM, 
PM2208, PM2202, 74HC595, MIPS 24KEc, SPI Flash, Unbrick, Shayan Shirazi.
```

---

## 📄 License

This project is licensed under the **GNU General Public License v2.0** (`GPL-2.0`). See the [LICENSE](./LICENSE) and [COPYING](./COPYING) files for full details.
