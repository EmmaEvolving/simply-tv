# Simply TV — Product Requirements

## Purpose

This document defines what Simply TV is and what it explicitly is not. Every feature decision flows from a single principle: the television exists to serve the person watching it.

---

## Non-Requirements (What Simply TV Will Never Have)

These are not missing features. They are deliberate omissions. Any fork or derivative that adds these items should not call itself Simply TV.

- WiFi or Ethernet connectivity of any kind
- Bluetooth (reserved exception: future keyboard/accessibility accessory only)
- An operating system capable of running third-party applications
- An app store or streaming application of any kind
- Automatic Content Recognition (ACR) — the technology that identifies what you watch
- Usage telemetry, analytics, or any data transmitted to any server
- A manufacturer account or login requirement
- Automatic firmware updates without explicit user initiation and confirmation
- Remote management capability of any kind
- A microphone
- A camera
- Advertising of any kind
- A terms of service agreement required for basic operation
- Any feature that requires an internet connection to function

---

## Requirements

### Display
- Primary panel technology: OLED (WRGB or QD-OLED)
- Fallback panel technology: LED/LCD (full-array local dimming preferred)
- Minimum panel size: 55 inches (diagonal)
- Minimum resolution: 3840 × 2160 (4K UHD)
- Minimum refresh rate: 120Hz
- HDR support: HDR10 mandatory, Dolby Vision optional
- Color gamut: minimum 90% DCI-P3 coverage
- OLED burn-in protection: mandatory, fully documented, open source

### Video Inputs
- HDMI 2.1: 4 ports minimum
  - Port 1: designated ARC/eARC
  - All ports: 4K 120Hz, HDR10, VRR (FreeSync/G-Sync compatible signaling)
  - HDCP 2.2 support on all ports
- Component video (Y/Pb/Pr): 1 input
  - HD support: 720p and 1080i mandatory
  - SD support: 480i and 480p
- Composite video (CVBS): 1 input
  - NTSC, PAL, SECAM autodetection
- RF/Coax (F-connector): 1 input
  - ATSC 3.0 demodulation
  - ATSC 1.0 (8-VSB) demodulation
  - Clear QAM cable (optional)
  - DRM-encrypted ATSC 3.0: explicitly not supported

### Audio Inputs
- HDMI audio: extracted from all HDMI inputs (PCM, Dolby Digital, DTS, Dolby Atmos passthrough)
- Analog stereo RCA: paired with each analog video input (composite and component)
- ATSC broadcast audio: AC-3 decode mandatory, AC-4 passthrough to eARC

### Audio Outputs
- Internal speakers: stereo, minimum 2 × 10W, Class D amplification
- TOSLINK optical: S/PDIF, PCM and compressed bitstream passthrough
- eARC (HDMI port 1): full format passthrough including lossless formats
- 3.5mm headphone jack: stereo, auto-mute on insertion, independent volume
- CEC: volume control passthrough to connected soundbar/receiver

### User Interface
- IR remote control: NEC or RC-5 protocol, 20-button minimum
- On-screen display: input selection, picture settings, audio settings, system settings
- No touchscreen requirement
- No voice control
- No motion control

### Connectivity
- USB-A: 2 ports, firmware update only
  - Explicitly not for media playback
  - Explicitly not for app installation
  - USB storage mounting for firmware update package only

### Firmware
- Source code: fully open (this repository, MIT license)
- Updates: USB-only, user-initiated, cryptographically signed
- Boot time: under 5 seconds to first picture
- Settings persistence: JSON config file, human-readable, user-editable
- No network stack in firmware (no WiFi driver, no TCP/IP, no DNS)
- Firmware update package includes human-readable changelog

### Physical
- VESA mount: 400×400mm minimum
- Stand: included, removable
- Dimensions: standard for panel size (no artificial thickness inflation)
- Connectors: rear-facing, labeled, accessible without moving the TV
- Power: standard IEC C13 inlet, universal voltage (100–240V AC)
- Remote storage: dedicated slot or holder on TV body (optional but preferred)

### Certifications (required for sale in US)
- FCC Part 15 (electromagnetic emissions)
- UL or ETL (electrical safety)
- HDMI LLC licensing
- Energy Star (strongly recommended)
- ATSC 3.0 certification (for tuner functionality)

---

## Design Priorities

When requirements conflict, resolve in this order:

1. User privacy and control
2. Picture quality
3. Reliability and longevity
4. Cost
5. Ease of manufacture

A cheaper component that adds any form of network connectivity or telemetry is never acceptable regardless of cost savings.

---

## Out of Scope for v1

These items are explicitly deferred. They may be addressed in future revisions.

- 8K resolution
- MicroLED panel support
- More than 4 HDMI ports
- Dolby Vision certification (HDR10 is mandatory, Dolby Vision is optional)
- Non-US regional certifications (CE, RCM, etc.) — documented but not validated
- Built-in subwoofer
- Soundbar integration beyond CEC
