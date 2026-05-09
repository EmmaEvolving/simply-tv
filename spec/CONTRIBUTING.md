# Contributing to Simply TV

Thank you for your interest in contributing. Simply TV is a design document first and a firmware project second — contributions to both are equally valued.

---

## What We Need Most

### Hardware and Design
- **BOM pricing corrections** — component prices change. If you see an outdated price, open a PR with current Digi-Key or Mouser pricing and a source link.
- **Schematic review** — the analog input, RF tuner, and audio sections especially benefit from experienced RF and analog engineers reviewing for errors.
- **PCB layout guidance** — documented rules for Vx1, HDMI, and RF trace layout.
- **Panel sourcing** — contacts, pricing, or datasheet information for LG Display or Samsung Display OLED panels at any volume.
- **Regional certifications** — CE marking, Australian RCM, Canadian ICES — documented requirements, testing labs, and cost estimates for non-US markets.

### Firmware (CM4 Path)
- **GStreamer pipeline optimization** — reducing input switching latency, improving format handling.
- **OLED protection algorithm tuning** — the ABL curves and pixel shift parameters are starting points. Real-world calibration data from actual OLED panels is needed.
- **Boot time reduction** — getting under 4 seconds reliably across different CM4 configurations.
- **IR remote keymaps** — additional remote control compatibility beyond the bundled remote.
- **Testing** — unit tests for OSD state machine, OLED protection algorithms, and firmware update verification.

### Documentation
- **Clarity edits** — if a section is unclear or technically imprecise, open an issue or PR.
- **Diagrams** — signal flow diagrams, block diagrams, and PCB layout illustrations.
- **ATSC 3.0 patent landscape** — this area needs careful legal documentation. If you have expertise here, please contribute.

---

## How to Contribute

### For documentation and design changes
1. Fork the repository
2. Create a branch: `git checkout -b your-topic`
3. Make your changes
4. Open a pull request with a clear description of what changed and why

### For firmware changes
1. Fork the repository
2. Create a branch: `git checkout -b firmware/your-feature`
3. Write tests for any new functionality
4. Ensure existing tests pass
5. Open a pull request

### For issues
- Use GitHub Issues for bug reports, design questions, and feature discussions
- Label issues appropriately: `hardware`, `firmware`, `documentation`, `question`
- For technical disagreements, include sources and reasoning — this is an engineering project

---

## What We Will Not Accept

Pull requests that add any of the following will be closed without merge:

- Network connectivity of any kind (WiFi, Ethernet, Bluetooth for anything other than accessibility)
- Telemetry, analytics, or usage tracking
- App frameworks or media streaming capabilities
- Automatic update mechanisms that do not require explicit user confirmation
- Any feature that requires an internet connection to function
- Proprietary or NDA-protected documentation or code

If you want to build a smart TV, this is not the repository for that. There are many others.

---

## Code Style

For firmware (C):
- C11 standard
- 4-space indentation, no tabs
- Function names: `snake_case`
- Constants: `SCREAMING_SNAKE_CASE`
- Every public function documented with a comment block
- No dynamic memory allocation in the main video/audio path (real-time sections must be allocation-free)

For documentation (Markdown):
- One sentence per line in source (makes diffs readable)
- Headers use sentence case, not Title Case
- Technical terms on first use should be explained inline — do not assume reader knowledge

---

## License

By contributing to Simply TV you agree that your contributions are licensed under the MIT License. Your name will appear in the git history and you are welcome to add yourself to a CONTRIBUTORS file.

Copyright of the overall work remains with the original author per the MIT license terms.
