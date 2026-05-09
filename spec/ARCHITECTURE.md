# Simply TV — System Architecture

This document is the system-level overview of Simply TV. It describes how the subsystems fit together, how signals flow from input to output, and how the four implementation paths relate to each other. It is the starting point for understanding the design — not the ending point. Every subsystem has a dedicated deep-dive document linked from this one.

---

## Design Premise

Simply TV is a display with inputs. Every architectural decision follows from that premise. There is no application platform, no network stack, no persistent user identity, and no communication with any external server. The signal path is: source in, picture out.

The architecture is intentionally conservative. Proven interfaces are preferred over newer ones. Discrete subsystems are preferred over integrated platforms that bundle unnecessary capability. Where a choice exists between a simpler design and a more capable one, simplicity wins unless the capability directly improves picture or sound quality.

---

## System Block Diagram

```
                        ┌─────────────────────────────────────────────────┐
                        │                  SIMPLY TV                       │
                        │                                                  │
  Antenna ─────────────►│  ATSC Tuner ──────────────────────────────────┐ │
                        │  (RF → Transport Stream)                       │ │
  HDMI 1 (eARC) ───────►│                                                │ │
  HDMI 2 ───────────────►│  HDMI Receivers                               │ │
  HDMI 3 ───────────────►│  (TMDS → pixel data + audio)                  │ │
  HDMI 4 ───────────────►│                                                │ │
                        │                                                 ▼ │
  Component ────────────►│  Analog Video Decoder ──────► MAIN PROCESSOR ───►│ OLED Panel
  (Y/Pb/Pr)             │  (ADV7183B)               │   (Scaler SoC      │ │ (primary)
                        │                            │    or CM4)         │ │
  Composite ────────────►│  Analog Video Decoder ──┘   │                  │ │ LED/LCD Panel
  (CVBS)                │  (shared ADV7183B)            │                  │ │ (fallback)
                        │                               │                  │ │
  RCA Audio L/R ────────►│  Audio ADC ────────────────► │                  │ │
                        │  (PCM1802)                    │                  │ │
                        │                               ▼                  │ │
                        │                         Audio Engine ────────────┘ │
                        │                               │                    │
                        │                               ├──► Internal Speakers│
                        │                               ├──► TOSLINK Out      │
                        │                               ├──► eARC (HDMI 1)   │
                        │                               └──► Headphone Jack   │
                        │                                                     │
                        │  IR Receiver ──────────────► Main Processor         │
                        │  USB (2×) ──────────────────► Firmware Update Only  │
                        │  Power Supply ──────────────► All subsystems        │
                        └─────────────────────────────────────────────────────┘
```

---

## Signal Flow Summary

### Video path

Every video input is converted to a common internal digital format before reaching the main processor. The main processor scales the signal to the panel's native resolution and drives the panel output interface.

| Input | Front-end chip | Internal format | Notes |
|---|---|---|---|
| HDMI 1–4 | HDMI receiver (in SoC) | YCbCr 4:2:2 or 4:4:4 | Up to 4K 120Hz HDR |
| Component HD | ADV7183B | ITU-R BT.656 | 720p, 1080i |
| Component SD | ADV7183B | ITU-R BT.656 | 480i, 480p |
| Composite | ADV7183B | ITU-R BT.656 | NTSC/PAL/SECAM auto |
| ATSC tuner | Si2158A + SL3000 | MPEG Transport Stream | Decoded by main processor |

### Audio path

All audio sources are converted to PCM on the I2S bus inside the main processor. Compressed formats (Dolby Digital, DTS) are decoded for internal speakers and passed through as bitstreams to TOSLINK and eARC outputs simultaneously.

| Source | Format in | Speakers | TOSLINK | eARC |
|---|---|---|---|---|
| HDMI (PCM) | LPCM stereo | PCM decoded | PCM | PCM |
| HDMI (Dolby Digital) | AC-3 bitstream | Decoded to PCM | AC-3 passthrough | AC-3 passthrough |
| HDMI (Dolby Atmos) | TrueHD/MAT | Decoded to PCM stereo | Not supported | Full passthrough |
| HDMI (DTS) | DTS bitstream | Decoded to PCM | DTS passthrough | DTS passthrough |
| ATSC 1.0 | AC-3 | Decoded to PCM | AC-3 passthrough | AC-3 passthrough |
| ATSC 3.0 | AC-4 | Decoded to PCM stereo | Not supported | AC-4 passthrough |
| Analog RCA | Analog stereo | PCM (via ADC) | PCM | PCM |

TOSLINK bandwidth limits lossless formats. Dolby Atmos and DTS:X in their full object-based forms require eARC. This is a physical limitation of the S/PDIF interface, not a design choice.

### Control path

The IR remote receiver outputs pulse trains on a GPIO pin. The main processor decodes NEC or RC-5 protocol and maps button codes to system actions. No other control input exists. CEC on HDMI is implemented for soundbar volume control only.

---

## Subsystems

### Main Processor

The central component of Simply TV. Receives all decoded video inputs, performs scaling to panel resolution, applies HDR tone mapping, and drives the panel output interface. Also manages audio routing, OSD rendering, IR decoding, settings persistence, and OLED panel care algorithms.

Four implementations are documented. See [Implementation Paths](#implementation-paths) below.

**Deep dive:** See `docs/firmware/FIRMWARE-OVERVIEW.md` (CM4 path) and subsystem docs for hardware detail.

### Analog Input Front End

Converts analog video signals (composite CVBS and component Y/Pb/Pr) from RCA connectors into digital video on the ITU-R BT.656 bus. The ADV7183B handles both input types on a single chip, with HD component support up to 1080i. Signal conditioning, ESD protection, 75Ω termination, and DC blocking are all handled before the decoder chip.

**Deep dive:** `docs/ANALOG-INPUT.md`

### ATSC Tuner Front End

Two-chip solution: the Silicon Labs Si2158A RF tuner downconverts the antenna signal to an intermediate frequency, and the Saankhya Labs SL3000 demodulator recovers the MPEG Transport Stream. Supports ATSC 1.0 (8-VSB) and ATSC 3.0. DRM-encrypted ATSC 3.0 content is explicitly not supported.

The RF section requires careful PCB layout: controlled-impedance 75Ω traces, a Faraday shield over the tuner section, and separation from all digital signals on the board.

**Deep dive:** `docs/ATSC-TUNER.md`

### Audio Subsystem

All audio paths internal to Simply TV use I2S as the common digital transport. The main processor acts as I2S master. Analog inputs are digitized by the PCM1802 ADC. Internal speakers are driven by the TAS5805M Class D amplifier with integrated DSP for EQ and speaker protection. TOSLINK output uses the Toshiba TOTX147 transmitter. eARC is carried on the designated HDMI port. The 3.5mm headphone jack uses the TPA6132A2 amplifier with hardware insertion detection for automatic speaker muting.

**Deep dive:** `docs/AUDIO-PATHS.md`

### Panel Interface and Output

Simply TV is designed primarily for OLED panels with LED/LCD as a supported fallback. The panel interface depends on the implementation path:

- **Realtek / MediaTek path:** V-by-One HS (Vx1) to the panel TCON board. 16 lanes for 4K 120Hz 10-bit. Panel power managed by TI TPS65161 PMIC generating VGH, VGL, VCOM, and AVDD.
- **CM4 path:** HDMI output from the CM4 to the panel's onboard controller. Targets 42"–48" OLED monitor-grade panels with HDMI input. Larger TV panels require a DSI-to-Vx1 bridge chip.

OLED panels require active burn-in protection. Simply TV implements four protection layers in firmware: Automatic Brightness Limiter (ABL), pixel shift, static element detection, and panel refresh cycle. All are fully documented and open source.

**Deep dive:** `docs/PANEL-INTERFACE.md`, `docs/OLED-CARE.md`

### Power Supply

A single switching power supply module provides the primary DC rails, rated approximately 200W for a 55" OLED system. Secondary panel power rails are generated by on-board regulators from the primary supply.

**Deep dive:** `docs/POWER-SUPPLY.md` *(in progress)*

### OSD and User Interface

The on-screen display is rendered as a DRM overlay plane composited over the video output in hardware. No display server (X11 or Wayland) is used. The CM4 path implements the OSD using Cairo and Pango writing directly to a DRM userspace framebuffer. The OSD is driven by a state machine responding to IR remote events. Settings are stored as a human-readable JSON file. Firmware updates are initiated from the OSD after the user inserts a signed update package on a USB drive.

**Deep dive:** `docs/firmware/OSD.md`, `docs/firmware/FIRMWARE-OVERVIEW.md`

---

## Implementation Paths

Simply TV is documented across four implementation paths. All four implement the same external interface and meet the same product requirements. They differ in openness, cost, complexity, and suitability for different builders.

### Option A — Realtek RTD2795 *(Production path)*

A purpose-built display scaler SoC. Integrates HDMI receivers, Vx1 panel output, audio engine, and OSD hardware acceleration in a single chip. Firmware is written in C against Realtek's SDK, available under ODM partnership. This is the path a manufacturer would take to build Simply TV as a shipping product.

- Board BOM: ~$40–65
- Openness: Low — proprietary SDK, closed firmware
- Community buildable: No

### Option B — MediaTek MT5895 *(ODM path)*

The SoC used in most current smart TVs, used here with the application platform stripped. More capable than needed but accessible via existing ODM supply chains. Reference boards exist and can be sourced. The firmware runs on a stripped Linux stack with all smart TV features disabled.

- Board BOM: ~$70–110
- Openness: Low — proprietary SDK
- Community buildable: No

### Option C — Raspberry Pi CM4 *(Open source path)*

Fully open hardware and software. The entire firmware stack is in this repository. HDMI inputs are captured via Toshiba TC358743 bridge chips on the CSI-2 interface. The GStreamer pipeline handles all video routing. The CM4's VideoCore VI hardware decoder handles HEVC and MPEG-2. Targets 42"–48" OLED panels with HDMI input. This is the path any engineer can build independently.

- Board BOM: ~$125–165
- Openness: High — all firmware in this repository, MIT license
- Community buildable: Yes
- Known limitation: ~2–3 frame input latency vs dedicated scaler

### Option D — ARM + FPGA *(Clean sheet design)*

A full custom design using an STM32H7 microcontroller and Lattice ECP5 FPGA implementing the scaler pipeline in RTL. Maximum transparency, maximum complexity. Documented primarily for educational value and as a reference for a truly from-scratch implementation.

- Board BOM: ~$125–185
- Openness: Very high — RTL source, full schematic
- Community buildable: Possible, multi-year engineering effort

### Comparison

| | Option A | Option B | Option C | Option D |
|---|---|---|---|---|
| **Total unit cost (est.)** | ~$305–465 | ~$335–510 | ~$390–565 | ~$390–585 |
| **Input latency** | <1 frame | <1 frame | 2–3 frames | <1 frame |
| **Max panel size** | 75"+ | 75"+ | 48" practical | 75"+ |
| **Firmware in this repo** | No | No | Yes | Partial |
| **ODM required** | Yes | Yes | No | No |
| **Recommended for** | Manufacturing | Prototyping | Community builds | Research |

---

## Interface Summary

### External interfaces (rear panel)

| Interface | Standard | Count | Notes |
|---|---|---|---|
| HDMI | 2.1 | 4 | Port 1 is ARC/eARC designated |
| F-connector (coax) | 75Ω | 1 | ATSC 3.0 / 1.0 |
| Component video | Y/Pb/Pr RCA | 1 set | HD up to 1080i |
| Composite video | CVBS RCA | 1 | Yellow RCA |
| Analog audio in | Stereo RCA | 2 pairs | Paired with component and composite inputs |
| TOSLINK | S/PDIF optical | 1 | PCM and compressed bitstream |
| 3.5mm headphone | TRS stereo | 1 | Auto-mutes speakers on insertion |
| USB-A | USB 2.0 | 2 | Firmware update only |

### Internal interfaces (board-to-board)

| Interface | Used for | Notes |
|---|---|---|
| I2S | All audio between subsystems | Main processor is I2S master |
| I2C | Control of peripheral ICs | ADV7183B, PCM1802, TAS5805M, TPA6132A2, tuner |
| ITU-R BT.656 | Analog decoder → main processor | Parallel 8-bit digital video |
| MPEG TS | Tuner demodulator → main processor | Serial transport stream |
| Vx1 HS (Options A/B) | Main processor → panel TCON | 16 lanes, 4K 120Hz 10-bit |
| HDMI (Option C) | CM4 → panel | Standard HDMI to panel's onboard controller |
| CSI-2 (Option C) | TC358743 → CM4 | HDMI capture bridge to CM4 camera interface |
| GPIO | Power sequencing, IR, detection | Panel enable, headphone detect, IR data |
| SPI | Firmware flash (Options A/B) | External NOR flash |
| eMMC | OS and settings (Options B/C) | CM4 module or external eMMC |

---

## What Is Explicitly Not in This Architecture

The following are absent by design. They are not implementation gaps — they are product decisions documented in `spec/DECISIONS.md`.

- Network interface of any kind — no WiFi, no Ethernet, no Bluetooth
- Application processor running a guest OS — no Android TV, no Tizen, no webOS
- Microphone
- Camera
- USB host for media playback — USB ports exist only for firmware update
- Automatic firmware update — user-initiated, signed, USB-only
- Any communication with any external server, ever, for any reason

---

## Document Index

| Document | Contents |
|---|---|
| `spec/REQUIREMENTS.md` | Full product requirements and explicit non-requirements |
| `spec/DECISIONS.md` | Key design decisions with rationale and alternatives considered |
| `docs/ANALOG-INPUT.md` | Analog front end: ESD, termination, ADV7183B, audio ADC |
| `docs/ATSC-TUNER.md` | RF tuner, demodulator, ATSC 3.0 position, DRM discussion |
| `docs/AUDIO-PATHS.md` | All audio paths: RCA in, speakers, TOSLINK, eARC, headphone |
| `docs/PANEL-INTERFACE.md` | Vx1 interface, TCON, OLED/LCD power sequencing |
| `docs/OLED-CARE.md` | Burn-in protection: ABL, pixel shift, logo dimming, refresh cycle |
| `docs/POWER-SUPPLY.md` | Power architecture, rail map, sequencing *(in progress)* |
| `docs/CERTIFICATION.md` | FCC, UL, HDMI licensing, ATSC certification roadmap *(planned)* |
| `docs/firmware/FIRMWARE-OVERVIEW.md` | CM4 firmware stack, module map, build system *(planned)* |
| `docs/firmware/VIDEO-PIPELINE.md` | GStreamer architecture, input switching, TC358743 *(planned)* |
| `docs/firmware/OSD.md` | OSD rendering, state machine, Cairo/Pango *(planned)* |
| `docs/firmware/OLED-ALGORITHMS.md` | ABL, pixel shift, and logo detection implementation *(planned)* |
| `docs/firmware/BOOT.md` | Boot sequence, systemd configuration, timing targets *(planned)* |
| `docs/firmware/UPDATE.md` | USB update flow, ed25519 signing, A/B partitions *(planned)* |
| `hardware/bom/BOM-REALTEK.md` | Full BOM for Option A *(in progress)* |
| `hardware/bom/BOM-CM4.md` | Full BOM for Option C *(in progress)* |
