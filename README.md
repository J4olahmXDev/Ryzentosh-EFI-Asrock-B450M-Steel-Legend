# 🍎 Ryzentosh EFI — ASRock B450M Steel Legend
### macOS Tahoe 26.5.1 | OpenCore 1.0.7

![macOS](https://img.shields.io/badge/macOS-Ventura_13-orange?style=flat-square&logo=apple)
![macOS](https://img.shields.io/badge/macOS-Sonoma_14.0-orange?style=flat-square&logo=apple)
![macOS](https://img.shields.io/badge/macOS-Sonoma_14.4-orange?style=flat-square&logo=apple)
![macOS](https://img.shields.io/badge/macOS-Sequoia_15-orange?style=flat-square&logo=apple)
![macOS](https://img.shields.io/badge/macOS-Tahoe_26.5.1-orange?style=flat-square&logo=apple)
![OpenCore](https://img.shields.io/badge/OpenCore-1.0.7-blue?style=flat-square)
![SMBIOS](https://img.shields.io/badge/SMBIOS-MacPro7,1-grey?style=flat-square)
![Status](https://img.shields.io/badge/Status-Working-green?style=flat-square)

![About This Mac](Docs/Screenshots/B5CC167C-A4D2-4E5B-8B6B-86A746BA2519.webp)

> Upgraded from macOS Sonoma 14.0.2 → Tahoe 26.5.1

---

## 💻 Specs

| Component | Detail |
|-----------|--------|
| **CPU** | AMD Ryzen 5 2600 Six-Core (Pinnacle Ridge) |
| **GPU** | AMD Radeon RX 5500 XT 4GB (Navi 14) |
| **RAM** | 32GB DDR4 2400MHz |
| **Motherboard** | ASRock B450M Steel Legend (AM4) |
| **Storage** | WD Black SN750 500GB NVMe PCIe |
| **Audio** | Realtek ALC892 + AMD Navi 10 HDMI Audio |
| **LAN** | Realtek RTL8168 PCIe Gigabit |
| **Wi-Fi / BT** | Intel Dual Band Wireless-AC 8265 (PCIe) |
| **Bootloader** | OpenCore 1.0.7 |
| **SMBIOS** | MacPro7,1 |

---

## ✅ Working

- **GPU** — Metal 3 acceleration (RX 5500 XT requires patching via OCLP-Mod 3.1.9 after install)
- **Audio** — AppleALC layout-id 1 + OCLP audio patch
- **LAN** — Realtek Gigabit 1000baseT at full speed
- **Wi-Fi** — itlwm + HeliPort (working normally)
- **Bluetooth** — Intel BT 4.2 via USB header (requires USB mapping first)
- **USB** — 15 ports mapped with USBToolBox + UTBMap
- **iCloud / App Store / iMessage / FaceTime**
- **Sleep / Wake** — works fully, wakes properly

---

## ❌ Not Working

- **Handoff / Continuity** — requires an Apple Wi-Fi card (e.g. BCM94360)
- **AirportItlwm native** — development stalled since Sonoma 14.4; tried patching for the Intel AC 8265 card but it still doesn't work, use HeliPort instead

---

## 🛠️ Main Kexts

| Kext | Version | Purpose |
|------|---------|---------|
| Lilu | 1.6.x | Base |
| VirtualSMC | 1.3.x | SMC Emulation |
| WhateverGreen | 1.6.x | GPU |
| AppleALC | 1.9.x | Audio |
| RealtekRTL8111 | 2.4.x | LAN |
| itlwm | 2.3.0 | Wi-Fi |
| IntelBluetoothFirmware | 2.4.0 | Bluetooth |
| BlueToolFixup | 0.6.x | BT Fix |
| USBToolBox + UTBMap | — | USB Mapping |
| AMFIPass | 1.4.x | OCLP Support |
| RestrictEvents | 1.1.x | CPU Name Fix |
| NVMeFix | 1.1.x | NVMe Power |

---

## ⚙️ ACPI

### SSDT-ALL-For-B450M.aml
Instead of using separate SSDTs (SSDT-EC, SSDT-PLUG, SSDT-USBX, SSDT-USB-Reset, SSDT-HPET), all of them are merged into a single combined SSDT for simplicity.

| Section | Purpose |
|---------|---------|
| **EC (Embedded Controller)** | Adds a fake EC device (`ACID0001`) under `\_SB.PCI0.SBRG`. AMD motherboards don't expose a real EC the way Intel ones do, so macOS needs this fake EC for sleep/wake and power management to work correctly. |
| **HPET** | Patches the HPET device's `_CRS` and `_STA` methods to fix IRQ conflicts (IRQ 8 and 0) that cause boot panics or instability on AMD platforms. |
| **PLUG (CPU Power Management)** | Adds a `_DSM` method under `\_PR.C000` (CPU core 0), required for `AMDRyzenCPUPowerManagement.kext` to enable per-core power management/throttling on Ryzen CPUs. |
| **USBX (USB Power Properties)** | Adds a virtual `USBX` device under `\_SB` with a `_DSM` method defining USB sleep/wake current limits. Without this, USB devices may not get proper power during sleep or fail to wake the system. |
| **USB-Reset** | Patches `_STA` on the XHCI USB controllers (`\_SB.PCI0.GP17.XHC0.RHUB` and `\_SB.PCI0.GPP2.PTXH.RHUB`) so macOS ("Darwin") sees them as enabled/disabled correctly — works alongside USBToolBox/UTBMap to ensure all 15 mapped USB ports are recognized. |

### ACPI Patches

| Comment | Find | Replace | Purpose |
|---------|------|---------|---------|
| HPET _STA to XSTA Rename | `_STA` | `XSTA` | Renames the original `_STA` method on HPET to `XSTA`, freeing up `_STA` so the SSDT-ALL HPET patch above can define its own without conflicting with the existing ACPI table. |
| HPET _CRS to XCRS Rename | `_CRS` | `XCRS` | Renames the original `_CRS` method on HPET to `XCRS`, for the same reason — clears the way for the SSDT-ALL HPET `_CRS` override to take effect. |

---

## 📋 BIOS Settings

**Disable:**
- Fast Boot
- Secure Boot
- CSM
- Above 4G Decoding (if issues occur)

**Enable:**
- EHCI/XHCI Hand-off
- OS Type: Other OS

---

## 🚀 Post-Install

1. Mount the EFI partition and copy the EFI folder onto it
2. Run **OCLP-Mod 3.1.9** → Apply Root Patch (GPU + Audio)
3. Install **HeliPort** for Wi-Fi
4. Map USB ports with USBToolBox on macOS

---

## ⚠️ Notes

- **Every time macOS updates**, re-run the OCLP-Mod root patch
- **SIP** must be set to `0x803` (csr-active-config = `03080000`)
- **amfi=0x80** must always be in boot-args for OCLP to work
- This EFI works on macOS Ventura 13, Sonoma 14.0, Sonoma 14.4, Sequoia 15, and Tahoe 26.5.1 — not limited to 26.5.1

---

## 🙏 Credits

- [Dortania OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)
- [AMD-OSX](https://github.com/AMD-OSX/AMD_Vanilla)
- [OCLP-Mod (laobamac)](https://github.com/laobamac/OCLP-Mod)
- [OpenIntelWireless](https://github.com/OpenIntelWireless/itlwm)
- [USBToolBox](https://github.com/USBToolBox/tool)
