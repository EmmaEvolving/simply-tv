# Simply TV — Design Decisions

This document records significant design decisions, the alternatives considered, and the reasoning behind each choice. It exists so that contributors understand *why* the design is the way it is, and so that future decisions can be made consistently.

---

## DD-001: Four implementation paths instead of one

**Decision:** Document four distinct implementation paths (Realtek, MediaTek, RPi CM4, ARM+FPGA) rather than committing to a single design.

**Rationale:** The goal of this repository is to make a dumb TV buildable by the widest possible range of people and organizations. A single design optimized for one use case would exclude others. A manufacturer needs the Realtek path. A hobbyist engineer needs the CM4 path. A researcher needs the FPGA path. Documenting all four costs more effort but produces a more useful artifact.

**Trade-off:** The repository is larger and more complex than a single-path design. Each path must be maintained independently.

---

## DD-002: OLED primary, LED/LCD fallback

**Decision:** Design primarily for OLED panels with LED/LCD as a documented fallback, rather than designing for LCD and treating OLED as an upgrade.

**Rationale:** OLED is the correct technology for this product. True black, infinite contrast, per-pixel HDR, and no backlight system to design eliminates an entire hardware subsystem. The audience for Simply TV — people who care enough about their television experience to seek out a non-smart option — is exactly the audience that values and understands OLED. Designing for the better technology and falling back to the acceptable one is the right order.

**Trade-off:** OLED panel sourcing is harder than LCD. OLED requires active burn-in protection firmware that LCD does not. OLED costs more.

---

## DD-003: ATSC 3.0 without DRM

**Decision:** Include ATSC 3.0 hardware capability but explicitly not implement the A3SA DRM framework or any IP-connected ATSC 3.0 features.

**Rationale:** ATSC 3.0's broadcast signal is a public resource. The DRM layer is a manufacturer and broadcaster addition on top of that signal. Implementing DRM would require certification, key provisioning infrastructure, and ongoing license compliance — all of which create dependencies on third parties that conflict with the Simply TV philosophy. Unencrypted ATSC 3.0 content provides OTA 4K reception. The FCC's simulcast requirement means all content remains available in ATSC 1.0 until at least 2027.

**Trade-off:** DRM-encrypted ATSC 3.0 content from broadcasters who have chosen to encrypt cannot be received. This is documented as a deliberate limitation, not a bug.

---

## DD-004: MIT license over GPL

**Decision:** MIT license rather than GPL v2 or v3.

**Rationale:** The goal is for someone to actually build this thing. GPL creates friction for commercial manufacturers who cannot open-source their entire manufacturing stack. MIT removes that barrier while still requiring attribution. The attribution requirement (copyright notice must be preserved in all forks) is what matters for this project — the viral copyleft of GPL is not needed and may be counterproductive.

**Trade-off:** Someone could theoretically take this design, build a proprietary product from it, and not contribute improvements back. We accept this trade-off in exchange for maximum adoption potential.

---

## DD-005: No network stack in CM4 firmware

**Decision:** The CM4 Linux build explicitly does not include WiFi drivers, NetworkManager, or any TCP/IP networking capability — even though the CM4 module with WiFi (CM4 with wireless) is capable of it.

**Rationale:** Specifying the CM4 without WiFi variant (CM4104032) and building a kernel without network drivers is the only way to make the "no network" guarantee credible and auditable. A firmware image that could connect to the internet but has networking disabled in software is not trustworthy. A firmware image that has no network drivers cannot connect to the internet regardless of software configuration.

**Implementation:** Use CM4 Lite variants without wireless (model numbers ending in no wireless suffix). Kernel config explicitly excludes all WiFi, Bluetooth, and Ethernet drivers. No NetworkManager, no wpa_supplicant, no DHCP client in the build.

---

## DD-006: Cairo/Pango for OSD rendering, no display server

**Decision:** OSD is rendered directly to a DRM overlay plane using Cairo and Pango, without X11 or Wayland.

**Rationale:** A display server adds complexity, latency, and attack surface that provides no benefit for a TV OSD. Cairo + Pango writing directly to a DRM userspace framebuffer is simpler, faster to start up, and fully auditable. The OSD requirements (text, rectangles, sliders, transparency) do not require GPU acceleration or a full compositing window system.

**Trade-off:** Developing the OSD requires working with DRM/KMS directly, which is less approachable than Qt or GTK. This is the right trade for a project targeting engineers.

---

## DD-007: USB firmware updates only, signed with ed25519

**Decision:** Firmware updates are delivered via USB drive only, require explicit user confirmation on screen, and are cryptographically verified with ed25519 signatures before installation.

**Rationale:** Over-the-air firmware updates are how smart TV manufacturers silently change behavior, add features users didn't consent to, and in extreme cases brick devices. USB-only updates require physical access and deliberate user action. ed25519 signatures prevent corrupted or malicious firmware from being installed accidentally. The signing key is held by the project maintainer; the public key is embedded in the firmware.

**Community consideration:** The key policy is documented openly. Community forks may generate their own signing keys. Users who want to run community firmware may install a custom public key via a documented process. The signing requirement prevents accidents, not legitimate community development.

---

## DD-008: ADV7183B over ADV7180 for analog input

**Decision:** Specify the Analog Devices ADV7183B as the analog video decoder rather than the more common ADV7180.

**Rationale:** The ADV7180 supports composite and standard-definition component video (480i/576i) only. The ADV7183B additionally supports HD component video (720p, 1080i). Including component inputs without HD support defeats the purpose — the primary reason to have component on a modern TV is for HD sources (original Xbox, PS2/PS3, Wii, cable boxes without HDMI). The ADV7183B costs marginally more and is pin-compatible with the ADV7180.