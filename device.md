# Orange Pi 4 Pro Technical Context & Reference

This document provides a consolidated overview of the Orange Pi 4 Pro based on the official User Manual (v1.4) and system image analysis. It serves as a technical reference for hardware specifications, interface mapping, and operational procedures.

## 1. Hardware Specifications

The Orange Pi 4 Pro is a high-performance open-source single-board computer powered by the Rockchip RK3399 SoC.

| Component | Specification |
 | ----- | ----- |
| **SoC** | Rockchip RK3399 (Big.Little architecture) |
| **CPU** | 6-core 64-bit (2x Cortex-A72 @ 1.8GHz + 4x Cortex-A53 @ 1.4GHz) |
| **GPU** | ARM Mali-T860 MP4 (Supports OpenGL ES1.1/2.0/3.0/3.1, OpenVG1.1, OpenCL, DX11) |
| **RAM** | 4GB LPDDR4 |
| **Storage** | MicroSD (TF) Card Slot, 16GB EMMC Flash (on-board) |
| **Wireless** | Dual-band Wi-Fi (2.4G/5G), Bluetooth 5.0 (CDW.20C4600-00 chip) |
| **Ethernet** | 10/100/1000Mbps RJ45 (YT8531C chip) |
| **Power** | 5V / 3A via USB Type-C or DC 5.5\*2.1mm Jack |
| **PCB Size** | 91mm x 56mm |

## 2. Interface Details & Layout

The board is designed for versatility with high-speed data and multimedia interfaces.

### Video & Display

* **HDMI 2.0:** Supports up to 4K @ 60fps.
* **Dual MIPI DSI:** 2x 4-lane MIPI DSI interfaces (Display 0 and Display 1).
* **USB Type-C DP:** The Type-C port supports DisplayPort 1.2 output (up to 4K @ 60fps).

### Camera (MIPI CSI)

* **CSI0 & CSI1:** Two 4-lane MIPI CSI camera inputs, supporting up to 13MP sensors. Supports dual camera simultaneous input.

### Audio

* **Output:** 3.5mm Headphone jack and HDMI audio.
* **Input:** Integrated on-board microphone and support for MIC input via the headphone jack.

### Data & Expansion

* **USB Ports:** 2x USB 2.0 Host, 1x USB 3.0 Host, 1x USB 3.0 Type-C.
* **Mini PCIe:** Used for 4G LTE modules or other PCIe expansions.
* **SIM Card Slot:** Micro-SIM slot (required for Mini PCIe LTE modules).

## 3. 40-Pin GPIO Pinout Reference

The 40-pin header is color-coded and compatible with many Raspberry Pi accessories.

| Pin | Function | Pin | Function |
 | ----- | ----- | ----- | ----- |
| 1 | 3.3V Power | 2 | 5V Power |
| 3 | I2C7_SDA | 4 | 5V Power |
| 5 | I2C7_SCL | 6 | GND |
| 7 | GPIO1_A0 | 8 | UART2_TX |
| 9 | GND | 10 | UART2_RX |
| 11 | GPIO1_A1 | 12 | GPIO1_A2 |
| 13 | GPIO1_A3 | 14 | GND |
| 15 | GPIO1_A4 | 16 | GPIO1_A5 |
| 17 | 3.3V Power | 18 | GPIO1_A6 |
| 19 | SPI1_TXD | 20 | GND |
| 21 | SPI1_RXD | 22 | GPIO1_A7 |
| 23 | SPI1_CLK | 24 | SPI1_CS |
| 25 | GND | 26 | GPIO4_C2 |
| 27 | I2C2_SDA | 28 | I2C2_SCL |

*Note: Debug UART (3-pin) is separate from the 40-pin header for serial console debugging.*

## 4. Base Image File Structure (Linux)

Official images (Ubuntu/Debian) follow a specific internal organization for kernels and overlays.

### Boot Directory (`/boot`)

Contains the kernel, initial RAM disk, and Device Tree Binaries (DTBs).

* **Kernel Image:** `vmlinux-5.15.147-sun60iw2` (or similar version).
* **Initrd:** `uInitrd-5.15.147-sun60iw2`.
* **DTB Path:** `/boot/dtb-5.15.147-sun60iw2/allwinner/`.
* **Overlays:** Located in `/boot/dtb-.../allwinner/overlay/`.

### System Configuration (`/etc`)

* **Netplan:** Uses Netplan for network orchestration via `NetworkManager` (config in `/etc/netplan/orangepi-default.yaml`).
* **SSH Config:** `/etc/ssh/sshd_config` (SSH is enabled by default).
* **Default User Info:** `/etc/sudoers.d/` contains permissions for the `orangepi` user.

## 5. Cloud-Init Configuration (Jammy Images)

Ubuntu 22.04 (Jammy) images for Orange Pi 4 Pro utilize `cloud-init` for automated provisioning and first-boot setup.

### Data Sources

* **NoCloud:** The default datasource is usually `NoCloud`, which looks for configuration in `/boot/armbian_first_run.txt` or `/boot/user-data`.

### Common Configuration Files

* **User Data:** `/etc/cloud/cloud.cfg` and files in `/etc/cloud/cloud.cfg.d/`.
* **Instance Data:** Found in `/var/lib/cloud/instance/`.

### First-Boot Features

* **Filesystem Expansion:** Automatically expands the root partition to fill the TF card/EMMC.
* **User Provisioning:** Sets up the default `orangepi` user, SSH keys, and locale.
* **Network Injection:** Can inject network configuration if provided via a `network-config` file in the boot partition.

### Troubleshooting Cloud-Init

* **Logs:** Check `/var/log/cloud-init.log` and `/var/log/cloud-init-output.log` for execution details.
* **Status:** Run `cloud-init status --wait` to verify completion.

## 6. Headless Operation & Remote Access

### Serial Console (Debug UART)

* **Settings:** Baud rate **1500000** (1.5Mbps), 8N1. Requires 3.3V TTL levels.

### Secure Shell (SSH)

* **Access:** `ssh orangepi@<ip_address>` (Password: `orangepi`).

## 7. Storage & Flashing Procedures

### Boot Priority

1. MicroSD (TF) Card $\to$ 2. EMMC Flash.

### Flashing EMMC

* **Method A:** Windows PC + RKDevTool + USB Type-C (Hold Recovery Key).
* **Method B:** SD "Burn" image (Automatic process).

## 8. Linux System Configuration

### Common Commands

* **Wi-Fi:** `nmcli device wifi connect "SSID" password "PASSWORD"`
* **Utility:** `orangepi-config` (Main system tool).
* **Thermal:** `cat /sys/class/thermal/thermal_zone0/temp` ($\text{value} / 1000 = ^\circ\text{C}$).

## 9. Critical Operating Notes

* **Power:** 5V/3A required.
* **Heat:** A72 cores require passive or active cooling under load.
