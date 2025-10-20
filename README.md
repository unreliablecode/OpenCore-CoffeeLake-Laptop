# OpenCore EFI for Coffee Lake Laptop

OpenCore EFI configuration targeting Intel 8th Generation Coffee Lake mobile platforms, tested and validated on Intel Core i7-8750H hardware (MSI GS65 Stealth Thin 8RF) running macOS Tahoe.

## macOS Compatibility

- macOS Tahoe (Tested and Working)
- macOS Sequoia (Compatible)
- macOS Sonoma (Compatible)
- macOS Ventura (Compatible)
- macOS Monterey (Compatible)

## Hardware Specifications

| Component | Hardware Details | Status |
| :--- | :--- | :--- |
| Laptop Model | MSI GS65 Stealth Thin 8RF | Compatible |
| Processor | Intel Core i7-8750H (6 Cores, 12 Threads, 2.20 GHz base, up to 4.10 GHz) | Working |
| Chipset | Intel HM370 Chipset (Coffee Lake-H) | Working |
| Integrated GPU | Intel UHD Graphics 630 | Working (Metal supported, full hardware acceleration) |
| Discrete GPU | NVIDIA GeForce GTX 1070 with Max-Q Design | Disabled via boot-args (`-wegnoegpu`) |
| Audio | Realtek High Definition Audio (ALC1220) | Working (`AppleALC.kext`, layout-id `34`) |
| Ethernet | Killer E2500 PCI Express Gigabit Ethernet | Working (`AtherosE2200Ethernet.kext`) |
| Wi-Fi | Intel Wireless-AC 9560 160MHz | Working (Native Wi-Fi via `AirportItlwm`, no HeliPort required) |
| Bluetooth | Intel Bluetooth Interface | Working (`IntelBluetoothFirmware.kext`, `BlueToolFixup.kext`) |
| Trackpad | Synaptics I2C Multi-Touch Trackpad | Working (`VoodooI2C.kext`, `VoodooI2CHID.kext`) |
| Keyboard | SteelSeries PS/2 Keyboard | Working (`VoodooPS2Controller.kext`) |
| Storage | NVMe M.2 Solid State Drive | Working (`NVMeFix.kext`) |

## Bootloader Information

- OpenCore Version: 1.0.0+
- Target Architecture: X64 (UEFI)
- Target SMBIOS: `MacBookPro16,4` (Alternative: `MacBookPro15,1`)

## Feature Status

### Working
- Full CPU power management and frequency scaling (`X86PlatformPlugin`)
- Intel UHD Graphics 630 hardware acceleration and smooth display brightness control
- Native Wi-Fi support via `AirportItlwm-Tahoe.kext` on macOS Tahoe, integrating directly into the macOS Control Center and status bar without requiring the HeliPort utility
- Built-in audio output, headphone jack auto-switching, and microphone input
- Bluetooth connectivity and device pairing
- Multi-touch gestures, tracking, and clicking via VoodooI2C
- Native keyboard mapping, backlight controls, and function shortcut keys
- Native battery status reporting and AC power adapter state detection
- System sleep, wake, and lid open/close transitions
- USB 3.1 / 3.0 / 2.0 port mappings and power delivery

### Unsupported / Work in Progress
- AirDrop: Not yet working. Due to inherent limitations of current Intel Wi-Fi and Bluetooth driver stacks on macOS, AirDrop discovery and transfers are not yet functional.
- Discrete NVIDIA GeForce GPU: Disabled. macOS does not support modern NVIDIA architectures. Disabling the dGPU ensures proper thermal management and battery runtime.
- External HDMI / Mini DisplayPort: Non-functional if physically routed through the discrete NVIDIA GPU.

## Network Configuration

### Native Wi-Fi (No HeliPort Required)
This EFI is configured with `AirportItlwm-Tahoe.kext` matched for macOS Tahoe (kernel 25.x+), alongside `itlwm.kext` for previous releases.
- Wi-Fi works natively using the built-in macOS menu bar and Control Center network selection.
- No third-party status bar client (such as HeliPort) is required to scan or connect to wireless networks.
- Location Services and native network prompts function as expected.
- Note: AirDrop and Apple Continuity features dependent on native Broadcom hardware are not yet supported.

## BIOS Configuration

### Recommended Settings to Disable
- Secure Boot
- Fast Boot
- Intel Platform Trust Technology (PTT) / SGX
- CFG Lock (if accessible in advanced BIOS menus; otherwise ensure `AppleXcpmCfgLock` quirk is active)
- VT-d (if enabled, ensure `DisableIoMapper` quirk is active)

### Recommended Settings to Enable
- UEFI Boot Mode
- SATA Mode: AHCI
- Intel Virtualization Technology (VT-x)
- Above 4G Decoding
- Hyper-Threading
- XHCI Hand-off

## Post-Installation Notes

### SMBIOS Generation
For security and privacy, generate a unique SMBIOS profile before using Apple ID services:
1. Download `GenSMBIOS`.
2. Generate values for `MacBookPro16,4` (or `MacBookPro15,1`).
3. Using OpenCore Auxiliary Tools or ProperTree, open `EFI/OC/config.plist`.
4. Update `PlatformInfo -> Generic`:
   - `SystemProductName`
   - `SystemSerialNumber`
   - `MLB`
   - `SystemUUID`
   - `ROM`

## Directory Structure

```text
EFI/
├── BOOT/
│   └── BOOTx64.efi
└── OC/
    ├── ACPI/
    │   └── MaLd0n.aml
    ├── Drivers/
    │   ├── HfsPlus.efi
    │   ├── OpenCanopy.efi
    │   ├── OpenRuntime.efi
    │   └── ResetNvramEntry.efi
    ├── Kexts/
    │   ├── AirportItlwm-Tahoe.kext
    │   ├── AirportItlwm.kext
    │   ├── AppleALC.kext
    │   ├── AtherosE2200Ethernet.kext
    │   ├── BlueToolFixup.kext
    │   ├── BrightnessKeys.kext
    │   ├── CpuTscSync.kext
    │   ├── ECEnabler.kext
    │   ├── IntelBluetoothFirmware.kext
    │   ├── IntelMausi.kext
    │   ├── itlwm.kext
    │   ├── Lilu.kext
    │   ├── NoTouchID.kext
    │   ├── NVMeFix.kext
    │   ├── RealtekRTL8111.kext
    │   ├── RestrictEvents.kext
    │   ├── SMCBatteryManager.kext
    │   ├── SMCLightSensor.kext
    │   ├── SMCProcessor.kext
    │   ├── USBInjectAll.kext
    │   ├── VirtualSMC.kext
    │   ├── VoodooI2C.kext
    │   ├── VoodooI2CHID.kext
    │   ├── VoodooPS2Controller.kext
    │   ├── WhateverGreen.kext
    │   └── XHCI-unsupported.kext
    ├── Resources/
    │   ├── Audio/
    │   ├── Font/
    │   ├── Image/
    │   └── Label/
    ├── OpenCore.efi
    └── config.plist
```

## Credits

- Acidanthera for OpenCorePkg and core kernel extensions (Lilu, VirtualSMC, WhateverGreen, AppleALC, VoodooPS2)
- Dortania for OpenCore installation and configuration documentation
- OpenIntelWireless for Intel Wi-Fi and Bluetooth drivers (itlwm, AirportItlwm, IntelBluetoothFirmware)
- Alexandre Da Silva for VoodooI2C
- MaLd0n / Olarila for ACPI patch baseline
