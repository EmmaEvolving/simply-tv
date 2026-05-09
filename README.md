# Simply TV

**An open system design for a television that does one thing: display a picture.**

No apps. No ads. No tracking. No firmware that updates itself overnight. No terms of service. No account required. Simply the picture.

---

## What This Is

Simply TV is an open source system design document for a privacy-first, dumb television. It is not a product you can buy — it is a complete technical specification that anyone can read, fork, contribute to, or use as the basis for actually building one.

Every modern "smart" TV ships with an operating system that tracks what you watch, serves ads, phones home to manufacturer servers, and updates itself without your consent. Simply TV is the answer to the question: *what would a television look like if it was designed for the person watching it, not the advertiser paying for their attention?*

The answer turns out to be simple. A display. Some inputs. A speaker. A remote. That's it.

---

## Why This Exists

In 2026 every major television manufacturer ships a product that watches you. Viewing habits are harvested, sold to advertisers, and used to serve targeted ads on the TV itself. Firmware updates arrive silently. Features are added and removed without consent. The television you bought is not the television you own.

The technology required to build a television that simply displays a picture has not changed. The industry chose to add surveillance because surveillance is profitable, not because it improves the viewing experience.

Simply TV is the specification for the television that should exist. If enough people build it, contribute to it, and share it, maybe it will.

---

## What's in This Repository

This repository contains the complete system design for Simply TV across four implementation paths, ranging from a production-ready proprietary scaler approach to a fully open source Raspberry Pi CM4 build that any engineer can reproduce.

```
simply-tv/
├── README.md                   ← You are here
├── LICENSE                     ← MIT License
│
├── spec/                       ← Master specification documents
│   ├── REQUIREMENTS.md         ← Product requirements and non-requirements
│   ├── ARCHITECTURE.md         ← System architecture overview
│   └── DECISIONS.md            ← Key design decisions and rationale
│
├── hardware/                   ← Hardware design documentation
│   ├── bom/                    ← Bills of materials for all four chip options
│   │   ├── BOM-REALTEK.md      ← Option A: Realtek RTD2795 (production path)
│   │   ├── BOM-MEDIATEK.md     ← Option B: MediaTek MT5895 (ODM path)
│   │   ├── BOM-CM4.md          ← Option C: Raspberry Pi CM4 (open source path)
│   │   └── BOM-CUSTOM.md       ← Option D: ARM + FPGA (clean sheet design)
│   ├── schematics/             ← Reference schematics (KiCad format)
│   │   ├── analog-input/       ← RCA composite and component front end
│   │   ├── atsc-tuner/         ← RF tuner and demodulator circuit
│   │   ├── audio/              ← Complete audio path (in and out)
│   │   └── panel-interface/    ← OLED panel power and interface
│   └── panel/
│       ├── OLED-SOURCING.md    ← OLED panel sourcing strategy
│       └── LED-FALLBACK.md     ← LED/LCD fallback panel notes
│
├── firmware/                   ← Firmware and software (CM4 open source path)
│   ├── kernel/
│   │   ├── config/             ← Minimal kernel configuration
│   │   └── overlays/           ← Device tree overlays
│   ├── tv-app/                 ← Main TV application (C)
│   │   ├── osd/                ← On-screen display (Cairo/Pango)
│   │   ├── video/              ← GStreamer pipeline management
│   │   ├── audio/              ← ALSA audio routing
│   │   ├── oled/               ← OLED burn-in protection algorithms
│   │   ├── input/              ← IR remote handling (evdev)
│   │   ├── settings/           ← JSON config persistence
│   │   └── update/             ← USB firmware update and verification
│   ├── scripts/
│   │   ├── build.sh            ← Build system
│   │   ├── flash.sh            ← eMMC programming
│   │   └── sign-update.sh      ← Firmware signing tool
│   └── tests/                  ← Unit and integration tests
│
└── docs/                       ← Deep dive documentation
    ├── ANALOG-INPUT.md         ← Analog front end deep dive
    ├── ATSC-TUNER.md           ← ATSC 1.0 and 3.0 tuner front end
    ├── AUDIO-PATHS.md          ← All audio paths (analog in/out, digital out)
    ├── PANEL-INTERFACE.md      ← Panel interface and output path
    ├── FIRMWARE-ARCH.md        ← OSD and firmware architecture (CM4)
    ├── OLED-CARE.md            ← Burn-in protection: what we do and why
    ├── POWER-SUPPLY.md         ← Power supply architecture
    ├── CERTIFICATION.md        ← FCC, UL, HDMI licensing roadmap
    └── CONTRIBUTING.md         ← How to contribute
```

---

## The Four Implementation Paths

Simply TV is designed across four levels of openness and complexity. Pick the one that matches your goals.

### Option A — Realtek RTD2795 *(Production Path)*
The chip inside most commercial monitors and displays today. Purpose-built scaler SoC with native HDMI 2.0, Vx1 panel output, and a complete TV signal chain. SDK available through ODM partnership. This is the path a real manufacturer would take to build Simply TV as a product.

**Best for:** Anyone planning to actually manufacture and sell units.

### Option B — MediaTek MT5895 *(ODM Path)*
The chip inside most current smart TVs — used here with all smart platform features disabled. More powerful than needed but accessible via existing ODM supply chains. Reference boards exist and can be sourced.

**Best for:** Fastest path to a working prototype via ODM engagement.

### Option C — Raspberry Pi CM4 *(Open Source Path)*
Fully open hardware and software. Every component is off-the-shelf, every line of firmware is in this repository, every design decision is documented. The trade-off: higher cost per unit, some latency overhead versus a dedicated scaler, and the OLED panel interface requires a bridge chip.

**Best for:** Engineers who want to build one themselves. The entire firmware section of this repo targets this path.

### Option D — ARM + FPGA *(Clean Sheet Design)*
A full custom design using an STM32H7 microcontroller and Lattice ECP5 FPGA implementing the scaler pipeline in RTL. Maximum transparency, maximum complexity. A multi-year engineering project but the most technically interesting path.

**Best for:** The design document. Understanding how a scaler works at the gate level.

---

## Design Philosophy

**Every feature is defined by what it lacks.**

Simply TV has no:
- WiFi or Ethernet
- Bluetooth (except optional for a future keyboard accessory)
- App store or streaming applications
- Automatic content recognition (ACR)
- Usage telemetry or analytics
- Manufacturer account requirement
- Auto-update without user confirmation
- Remote management or kill switch
- Voice assistant microphone

Simply TV has:
- HDMI 2.1 (4 ports, one with eARC)
- ATSC 3.0 / 1.0 tuner (OTA broadcast — free, local, private)
- Component video (Y/Pb/Pr) with HD support up to 1080i
- Composite video (CVBS)
- Optical TOSLINK audio output
- 3.5mm headphone jack
- USB-A (firmware update only — explicitly not for media playback apps)
- IR remote control
- CEC (for soundbar volume control)

---

## The ATSC 3.0 Position

ATSC 3.0 (NextGen TV) supports DRM encryption and IP-connected features that conflict with the Simply TV philosophy. Our position:

Simply TV implements ATSC 3.0 at the RF and demodulator level, receiving and decoding unencrypted broadcast signals and providing full OTA 4K reception where available. We deliberately do not implement the A3SA DRM framework, IP connectivity features, or any ATSC 3.0 interactive layer. The FCC requires broadcasters to simulcast in ATSC 1.0 through at least 2027, so all content remains accessible.

This means Simply TV cannot receive DRM-encrypted ATSC 3.0 content from broadcasters who have chosen to encrypt their signal. We consider this a feature. A television that requires a license server to display a broadcast signal is not a television — it is a terminal.

---

## OLED First

Simply TV is designed primarily for OLED panels with LED/LCD as a supported fallback. OLED is the right technology for this product: true black, infinite contrast, per-pixel HDR, no backlight to manage, and a simpler hardware output path.

OLED requires active panel care firmware. Unlike smart TVs that implement burn-in protection as an undocumented black box, Simply TV's OLED protection algorithms are fully documented and open source. See [OLED-CARE.md](docs/OLED-CARE.md) for the complete technical description of what we do and why.

The four active protection layers are:
- **Automatic Brightness Limiter (ABL)** — reduces brightness on large bright static areas
- **Pixel Shift** — moves the image 1–2 pixels at 4-minute intervals, invisibly
- **Static Element Detection** — identifies and dims persistent logos and UI elements
- **Panel Refresh Cycle** — triggers the panel's built-in compensation routine on standby

All four are enabled by default. Pixel shift and ABL cannot be disabled. Static element detection sensitivity is user-adjustable.

---

## Current Status

This is a living design document. Sections are added and refined over time.

| Section | Status |
|---|---|
| System architecture | ✅ Complete |
| Analog input path | ✅ Complete |
| ATSC tuner front end | ✅ Complete |
| Audio paths | ✅ Complete |
| Panel interface and output | ✅ Complete |
| OSD and firmware architecture (CM4) | ✅ Complete |
| Power supply architecture | 🔄 In progress |
| BOMs (all four options) | 🔄 In progress |
| Reference schematics | ⏳ Planned |
| Firmware implementation (CM4) | ⏳ Planned |
| Certification roadmap | ⏳ Planned |

---

## Contributing

Simply TV is an open design. Contributions are welcome across all areas — hardware corrections, firmware improvements, documentation clarity, BOM pricing updates, and regional regulatory guidance.

Please read [CONTRIBUTING.md](docs/CONTRIBUTING.md) before opening a pull request.

Areas where expertise is most needed:
- ATSC 3.0 regulatory and patent landscape
- Regional certifications outside the US (CE marking, Australia/NZ, etc.)
- PCB layout review for the analog and RF sections
- OLED panel sourcing contacts and volume pricing
- Firmware contributions to the CM4 implementation

---

## License

MIT License — see [LICENSE](LICENSE) for full text.

Copyright is retained by the original author. All forks and derivatives must preserve the copyright notice and license text per the MIT license terms.

---

## Acknowledgments

This design draws on publicly available application notes, datasheets, and technical documentation from Analog Devices, Texas Instruments, Silicon Labs, Saankhya Labs, Toshiba, Realtek, the Raspberry Pi Foundation, and the Linux kernel project. No proprietary or NDA-protected material is included in this repository.
