# AGENTS.md — Beelink SER5 Max Hackintosh

This repo is **not a software project**. It contains the **EFI partition** of an OpenCore-based Hackintosh. No code to build, test, lint, or run. There is no package manager, task runner, or language toolchain.

## Repo structure

```
EFI/
├── BOOT/BOOTx64.efi          # OpenCore bootloader
└── OC/
    ├── config.plist           # Active config
    ├── ACPI/                  # SSDT hotpatches (CPUR, EC, USB-Reset, USBW, USBX)
    ├── Drivers/               # UEFI drivers (36 .efi files)
    ├── Kexts/                 # 28 kernel extensions
    ├── Tools/                 # UEFI shell tools (OpenShell, CleanNvram, etc.)
    └── Resources/             # OpenCanopy GUI theme (DalianSky\BeeLink)
.github/workflows/
├── argus-review.yml           # CI: Argus-Flash review on PRs
└── pages-deploy.yml           # Deploy static GitHub Pages site
```

## Hardware

- **CPU**: AMD Ryzen 7 5800H (8C/16T) — extensive kernel patches (algrey's) required
- **GPU**: Integrated AMD Radeon (via NootedRed.kext, no dGPU)
- **Audio**: Realtek ALC (AppleALC_5800H.kext, layout-id=58)
- **Ethernet**: Realtek RTL8125 2.5G (RealtekRTL8111.kext)
- **WiFi/BT**: Intel AX200 (AirportItlwm)
- **SMBIOS**: iMac20,1

## Key config facts

### Boot args (from config.plist)
```
revpatch=sbvmm revblock=media keepsyms=1 alcid=58 darkwake=0 -NRedDPDelay
```

### AirportItlwm is version-specific
Kexts are loaded per macOS version via MinKernel/MaxKernel ranges:
- `AirportItlwm_for_Ventura.kext` — 22.0.0 to 22.99.99 (current)
- `AirportItlwm_for_Sonoma.kext` — 23.0.0 to 23.3.99
- `AirportItlwm_for_Sonoma_14.4.kext` — 23.4.0 to 23.99.99
- `AirportItlwm_for_Monterey.kext` — 21.0.0 to 21.99.99
- `AirportItlwm_for_BigSur.kext` — 20.0.0 to 20.99.99

### Smol BIOS / SIP settings
- `csr-active-config`: `AAAAAA==` (SIP fully enabled)
- `SecureBootModel`: `j160`
- `DummyPowerManagement`: true (required for AMD)
- `DisableIoMapper`: true (AMD GPU quirk)
- `ProvideCurrentCpuInfo`: true (AMD quirk)

### Not all kexts in the Kexts/ dir are loaded
Unused kexts inside `Kexts/`:
- `IO80211FamilyLegacy`, `IOSkywalkFamily` — not in Kernel.Add at all. Leftovers for BCM94360Z3 upgrade path.
- `AppleIGC` — in Kernel.Add but `Enabled=false`. Intel 2.5G NIC backup; the RTL8125 currently uses `RealtekRTL8111.kext`.

Do not enable them without testing.

## PlatformInfo sensitivity

`config.plist` contains real SMBIOS data (serial, MLB, ROM, UUID). These are **private to this machine**. Do not commit changed PlatformInfo values or publish them outside the repo.

## CI

- **Argus-Flash** (argus-review.yml): Runs on PR open/sync. Uses a GitHub App token; no manual setup needed.
- **Pages** (pages-deploy.yml): Deploys a static GitHub Pages site on push to `main` or manual dispatch. Copies only the root `index.html` and `.nojekyll` into `dist/` — it does not package the EFI binaries.

## What to update

To update OpenCore or kexts:
1. Replace `.efi` files in `BOOT/`, `OC/Drivers/`, `OC/Tools/`
2. Replace `.kext` bundles in `OC/Kexts/`
3. Update version comments in `OC/config.plist` (Kernel.Add entries)
4. Test boot before committing