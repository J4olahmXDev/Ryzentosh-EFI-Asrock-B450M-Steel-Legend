# 🍎 Ryzentosh EFI — ASRock B450M Steel Legend
### macOS Tahoe 26.5.1 | OpenCore 1.0.7

![macOS](https://img.shields.io/badge/macOS-Tahoe_26.5.1-orange?style=flat-square&logo=apple)
![OpenCore](https://img.shields.io/badge/OpenCore-1.0.7-blue?style=flat-square)
![SMBIOS](https://img.shields.io/badge/SMBIOS-MacPro7,1-grey?style=flat-square)
![Status](https://img.shields.io/badge/Status-Working-green?style=flat-square)

> อัพมาจาก macOS Sonoma 14.0.2 → Tahoe 26.5.1

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

## ✅ ใช้งานได้

- **GPU** — Metal 3 acceleration (RX 5500 XT ต้อง patch ผ่าน OCLP-Mod 3.1.9 หลังติดตั้ง)
- **Audio** — AppleALC layout-id 1 + OCLP audio patch
- **LAN** — Realtek Gigabit 1000baseT เต็มสปีด
- **Wi-Fi** — itlwm + HeliPort (ใช้งานได้ปกติ)
- **Bluetooth** — Intel BT 4.2 ผ่าน USB header (ต้อง USB map ก่อน)
- **USB** — 15 ports mapped ด้วย USBToolBox + UTBMap
- **iCloud / App Store / iMessage / FaceTime**
- **Sleep / Wake** — ปลุกตื่นครบ ใช้งานได้ปกติ

---

## ❌ ใช้ไม่ได้

- **Handoff / Continuity** — ต้องการ Apple Wi-Fi card (เช่น BCM94360)
- **AirportItlwm native** — หยุดพัฒนาตั้งแต่ Sonoma 14.4 การ์ด Intel AC 8265 ลอง patch แล้วยังใช้ไม่ได้ ใช้ HeliPort แทน

---

## 🛠️ Kexts หลัก

| Kext | Version | หน้าที่ |
|------|---------|--------|
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

## ⚙️ ACPI SSDTs (จาก SSDTTime)

| File | หน้าที่ |
|------|--------|
| SSDT-EC.aml | Fake EC |
| SSDT-PLUG.aml | CPU Power Management |
| SSDT-USBX.aml | USB Power Properties |
| SSDT-USB-Reset.aml | Reset USB Controllers |
| SSDT-HPET.aml | IRQ Fix |

---

## 📋 BIOS Settings

**Disable:**
- Fast Boot
- Secure Boot
- CSM
- Above 4G Decoding (ถ้ามีปัญหา)

**Enable:**
- EHCI/XHCI Hand-off
- OS Type: Other OS

---

## 🚀 Post-Install

1. Mount EFI และใส่ EFI folder
2. รัน **OCLP-Mod 3.1.9** → Apply Root Patch (GPU + Audio)
3. ลง **HeliPort** สำหรับ Wi-Fi
4. USB mapping ด้วย USBToolBox บน macOS

---

## ⚠️ หมายเหตุ

- **ทุกครั้งที่ macOS update** ต้องรัน OCLP-Mod root patch ใหม่
- **SIP** ต้องตั้งเป็น `0x803` (csr-active-config = `03080000`)
- **amfi=0x80** ต้องอยู่ใน boot-args เสมอเพื่อให้ OCLP ทำงาน
- Test บน **macOS Tahoe 26.5.1** เท่านั้น ไม่รับประกันเวอร์ชันอื่น

---

## 📸 Screenshots

> *แนบรูป About This Mac + Hackintool ที่นี่*

---

## 🙏 Credits

- [Dortania OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)
- [AMD-OSX](https://github.com/AMD-OSX/AMD_Vanilla)
- [OCLP-Mod (laobamac)](https://github.com/laobamac/OCLP-Mod)
- [OpenIntelWireless](https://github.com/OpenIntelWireless/itlwm)
- [USBToolBox](https://github.com/USBToolBox/tool)
