# Simply TV — Glossary

Technical terms, acronyms, and abbreviations used across the Simply TV design documentation. Entries are grouped by domain and listed alphabetically within each group. If a term appears in more than one domain it is listed where it is most relevant.

---

## Display and Panel

**ABL — Automatic Brightness Limiter**
A firmware-controlled protection feature for OLED panels. When a large proportion of the screen displays bright content simultaneously, ABL reduces overall panel brightness to stay within the panel's rated power envelope and limit accelerated pixel aging. A full-screen white image will appear dimmer than a small bright highlight against a dark background. Required on all OLED displays.

**APL — Average Picture Level**
The average luminance across an entire video frame, expressed as a percentage of maximum brightness. Used by the ABL algorithm to determine how much brightness reduction to apply. High APL (bright overall scene) triggers greater reduction than low APL (mostly dark scene with bright highlights).

**CRTC — Cathode Ray Tube Controller**
A legacy term retained in the Linux DRM/KMS API. In modern usage refers to the display controller pipeline — the hardware block that reads framebuffers and generates the timing signals for a display output. Simply TV's CM4 implementation uses the vc4 CRTC hardware.

**DCI-P3**
A color space standard defined by the Digital Cinema Initiatives. Covers a wider gamut than sRGB — more saturated reds and greens. Used as the reference color space for HDR content. Simply TV specifies minimum 90% DCI-P3 coverage for panel sourcing.

**DRM — Direct Rendering Manager**
The Linux kernel subsystem responsible for interfacing with GPU and display hardware. Provides a standardized API for display output, framebuffer management, and hardware compositing. Simply TV's CM4 firmware uses DRM directly, without a display server such as X11 or Wayland.

**eDP — Embedded DisplayPort**
A display interface standard derived from DisplayPort, designed for internal panel connections in laptops and monitors. Packet-based, supports high resolutions and refresh rates over a small number of differential lanes. Used for monitor-grade panels. Not standard on large-format TV panels, which typically use Vx1.

**FALD — Full-Array Local Dimming**
A backlight technique for LED/LCD panels where the LED backlight is divided into independently controllable zones. Zones behind dark image areas can be dimmed or turned off, improving perceived contrast ratio. Contrasted with edge-lit designs where LEDs are only on the panel perimeter.

**FMM — Fine Metal Mask**
A manufacturing technique used in OLED panel production. A thin metal stencil with microscopic holes is used to deposit RGB organic materials precisely on the substrate. Used by Samsung Display for QD-OLED production. LG Display's WRGB architecture avoids FMM for large panels.

**HDR — High Dynamic Range**
A display capability that allows simultaneous rendering of very dark and very bright image areas with greater accuracy than standard dynamic range (SDR). Requires both panel capability (high peak brightness, deep blacks) and content metadata. HDR10 is the baseline open standard. Dolby Vision is a proprietary HDR format with dynamic metadata.

**HDR10**
An open HDR standard using static metadata (a single brightness mapping for the entire content). Supported by all HDR-capable displays. Simply TV mandates HDR10 support.

**HVS — Hardware Video Scaler**
The hardware compositing and scaling block inside the Raspberry Pi's VideoCore GPU. Takes multiple input planes (video, OSD overlay), scales them to the output resolution, composites them with hardware alpha blending, and outputs the result to the HDMI encoder. Used in Simply TV's CM4 implementation.

**IPS — In-Plane Switching**
An LCD panel technology where liquid crystals rotate within the plane of the panel rather than perpendicular to it. Provides wider viewing angles and more accurate color than TN (Twisted Nematic) panels. An alternative to VA (Vertical Alignment) which offers higher contrast but narrower viewing angles.

**KMS — Kernel Mode Setting**
The Linux kernel feature that allows the display mode (resolution, refresh rate, color depth) to be configured from kernel space rather than userspace. Part of the DRM subsystem. vc4-kms-v3d is the relevant driver on the Raspberry Pi CM4.

**LVDS — Low-Voltage Differential Signaling**
A legacy high-speed serial interface standard used for internal panel connections in older LCD TVs and monitors. Limited bandwidth makes it unsuitable for 4K content. Superseded by eDP for monitors and Vx1 for TV panels.

**OLED — Organic Light-Emitting Diode**
A display technology where each pixel contains organic compounds that emit light when current is applied. Unlike LCD, OLED pixels are self-emissive — no backlight is required. Pixels displaying black simply receive no current, producing true black and theoretically infinite contrast ratio. Subject to differential aging (burn-in) which requires active firmware mitigation.

**OSD — On-Screen Display**
The graphical user interface overlaid on the video output. On Simply TV this includes input source labels, volume indicators, picture and audio settings menus, and the no-signal message. Implemented as a DRM overlay plane in the CM4 firmware.

**PPL — Peak Picture Level**
The maximum luminance value present in a video frame. Used alongside APL in ABL calculations.

**QD-OLED — Quantum Dot OLED**
Samsung Display's OLED architecture. Uses blue OLED emitters as the light source and quantum dot color conversion layers to produce red and green. Avoids the WRGB color filter approach used by LG Display. Offers higher color saturation and runs cooler than WRGB OLED.

**SDR — Standard Dynamic Range**
Conventional display capability with a limited luminance range, defined by the Rec. 709 standard. Contrasted with HDR.

**TCON — Timing Controller**
A board (supplied as part of the panel assembly) that sits between the main scaler board and the LCD glass. Receives the Vx1 or eDP signal from the main board, deserializes it, generates gate driver timing pulses, controls source drivers, and applies overdrive compensation. Not present in OLED panels — OLED driver boards perform an analogous function differently.

**TFT — Thin-Film Transistor**
The switching element at each pixel in an LCD display. Each pixel has one or more TFTs that control the voltage applied to the liquid crystal, thereby controlling the pixel's transmission. VGH and VGL supply voltages control TFT gate switching.

**VA — Vertical Alignment**
An LCD panel technology offering higher contrast ratios than IPS but with narrower viewing angles. Common in budget and mid-range TV panels.

**VGH — Gate-High Voltage**
The positive supply voltage applied to LCD gate driver ICs. Turns TFT transistors on, allowing pixel data to be written to each row. Typically +20 to +28V. Generated by the TPS65161 PMIC.

**VGL — Gate-Low Voltage**
The negative supply voltage applied to LCD gate driver ICs. Turns TFT transistors off between row writes. Typically −6 to −12V. Generated by the TPS65161 PMIC.

**VCOM — Common Electrode Voltage**
The voltage applied to the common electrode of an LCD panel. Must be precisely calibrated to minimize DC imbalance across the liquid crystal layer. Incorrect VCOM causes image sticking and accelerated aging. Calibrated during factory production and stored in PMIC EEPROM.

**Vx1 / V-by-One HS — V-by-One High Speed**
A high-speed serial interface standard developed by THine Electronics for large-format TV panel connections. Carries pixel data at up to 3.75 Gbps per lane. A 4K 120Hz 10-bit panel requires 16 lanes. The standard interface between the main board and TCON board in TV-grade LCD and OLED panels. Distinct from eDP, which is used in monitor-grade panels.

**WOLED / WRGB — White OLED / White-Red-Green-Blue**
LG Display's large-format OLED panel architecture. Uses white OLED subpixels (created by combining blue and yellow OLED emitters) with color filters on top to produce RGB output, plus an additional white subpixel for brightness. Easier to manufacture at large sizes than direct-emission RGB OLED. Used in Simply TV's primary panel sourcing target.

---

## Video Interfaces and Standards

**4:2:0, 4:2:2, 4:4:4 — Chroma Subsampling**
Notation describing how color information is sampled relative to luminance. 4:4:4 carries full color resolution. 4:2:2 halves horizontal color resolution. 4:2:0 halves color resolution in both dimensions. Broadcast and streaming content is typically 4:2:0 or 4:2:2. Studio production and some HDMI modes support 4:4:4.

**ARC — Audio Return Channel**
An HDMI feature allowing a TV to send audio to a connected soundbar or AV receiver over the same cable that brings video in. Supports PCM stereo and compressed formats including Dolby Digital 5.1. Superseded by eARC for higher-bandwidth formats. Simply TV supports ARC on HDMI port 1 for backward compatibility.

**BT.656 — ITU-R BT.656**
A standard for digital transmission of standard-definition video using an 8-bit parallel bus with embedded synchronization. The output format of the ADV7183B analog video decoder and the input format accepted by scaler SoCs for analog video. Carries 720×480i (NTSC) or 720×576i (PAL) at 27 MHz.

**BT.2020 — ITU-R BT.2020**
A color space standard covering a very wide gamut intended for UHDTV. Larger than DCI-P3. Used as the container color space for HDR10 content. Most current displays cannot reproduce the full BT.2020 gamut; content is tone-mapped to the display's actual capabilities.

**BT.709 — ITU-R BT.709**
The color space standard for HDTV and SDR content. The reference for standard dynamic range displays. Simply TV handles BT.709 content on all inputs.

**CEC — Consumer Electronics Control**
A single-wire protocol carried on HDMI pin 13. Allows connected devices to send control commands to each other over the HDMI cable. Common uses: TV remote controlling soundbar volume, AV receiver powering on with the TV, source switching notification. Simply TV implements a minimal CEC subset for soundbar volume control.

**CVBS — Composite Video Baseband Signal**
The technical name for standard composite video. A single analog signal carrying luminance, chrominance, sync, and blanking information combined on one wire. Nominally 1Vpp into 75Ω. Received on the yellow RCA connector.

**CSI-2 — Camera Serial Interface 2**
A MIPI Alliance serial interface standard for connecting image sensors and capture devices to application processors. On the Raspberry Pi CM4, the CSI-2 interface is normally used for camera modules. Simply TV's CM4 path uses it to receive the output of the TC358743 HDMI-to-CSI-2 bridge chip.

**eARC — Enhanced Audio Return Channel**
An HDMI 2.1 feature providing dramatically higher bandwidth than ARC. Supports Dolby TrueHD, Dolby Atmos, DTS:X, and 7.1 channel uncompressed PCM — formats that TOSLINK cannot carry. The preferred audio output connection for modern soundbars and AV receivers. Simply TV implements eARC on HDMI port 1.

**EDID — Extended Display Identification Data**
A data structure stored in a small EEPROM on the display, read by the source device over the HDMI DDC (I2C) channel on connection. Describes the display's capabilities: supported resolutions, refresh rates, color spaces, HDR support, and audio formats. The source uses EDID to determine what signal to send.

**HDCP — High-bandwidth Digital Content Protection**
A digital copy protection standard required by content license agreements for HDMI. Involves a cryptographic handshake between source and display before protected content is transmitted. HDCP 2.2 is required for 4K HDR content from streaming devices and UHD Blu-ray players. Simply TV implements HDCP 2.2 on all HDMI inputs.

**HDMI — High-Definition Multimedia Interface**
The standard consumer interface for digital audio and video. HDMI 2.1 (specified for Simply TV) supports up to 48 Gbps bandwidth, enabling 4K 120Hz, 8K 60Hz, variable refresh rate (VRR), and eARC. Governed by the HDMI Forum; use requires a license from HDMI LLC.

**PSIP — Program and System Information Protocol**
A standard embedded in ATSC broadcast streams that carries channel guide information: channel numbers, station names, program titles, ratings, and schedules. Simply TV parses PSIP to display channel names and program information in the OSD without requiring an internet-connected program guide.

**S/PDIF — Sony/Philips Digital Interface Format**
A consumer digital audio interface standard. Carries PCM audio or compressed bitstreams (Dolby Digital, DTS) using biphase mark coding. Implemented in Simply TV as TOSLINK optical output. Maximum bandwidth supports PCM stereo up to 192kHz/24-bit or compressed 5.1 formats. Cannot carry lossless formats such as Dolby TrueHD or DTS-HD Master Audio — these require eARC.

**TMDS — Transition-Minimized Differential Signaling**
The physical layer encoding used by HDMI and DVI for video data transmission. Uses differential pairs to minimize electromagnetic interference. An HDMI 2.1 connection has three TMDS data channels plus a clock channel, or uses FRL (Fixed Rate Link) mode for higher bandwidth.

**VRR — Variable Refresh Rate**
A display feature allowing the refresh rate to vary dynamically to match the frame output rate of the connected source. Reduces screen tearing in gaming applications. HDMI 2.1 standardizes VRR signaling. Simply TV passes VRR signaling through on all HDMI ports.

**Y/C — Luminance/Chrominance**
The separated luminance (Y) and chrominance (C) components of a video signal. S-Video carries Y/C on separate conductors, avoiding the Y/C mixing and separation required by composite video. The ADV7183B accepts S-Video input directly, though Simply TV's rear panel does not include S-Video connectors in the base specification.

**YCbCr**
A color encoding scheme used in digital video. Y is luminance. Cb and Cr are blue and red color difference components. Equivalent to Y/Pb/Pr in the analog domain. The internal digital video format used between the HDMI receivers and the scaler in Simply TV.

**YPbPr**
The analog equivalent of YCbCr. The color encoding used by component video (the three-RCA HD input). Y carries luminance and sync. Pb carries blue color difference. Pr carries red color difference. Decoded by the ADV7183B.

---

## Audio

**ABD — Audio Bit Depth**
The number of bits used to represent each audio sample. Higher bit depth provides greater dynamic range. Simply TV supports 16-bit and 24-bit PCM audio throughout the signal chain. The PCM1802 ADC and PCM5102A DAC are both 24-bit.

**AC-3 — Dolby Digital**
Dolby's compressed multi-channel audio codec used in ATSC 1.0 broadcast, DVD, and legacy Blu-ray. Supports up to 5.1 channels. Decoded internally in Simply TV for speaker output; passed through as a bitstream to TOSLINK and eARC outputs.

**AC-4**
Dolby's newer compressed audio codec used in ATSC 3.0 broadcast. Supports up to 7.1.4 Dolby Atmos object-based audio. Decoding AC-4 in the TV requires a Dolby license. Simply TV decodes AC-4 to PCM stereo for internal speaker output and passes it through to eARC for receiver-side decoding.

**ALSA — Advanced Linux Sound Architecture**
The Linux kernel's standard audio subsystem. Provides device drivers for audio hardware and a userspace API for audio applications. Simply TV's CM4 firmware uses ALSA for all audio device control and routing.

**BMC — Biphase Mark Coding**
The line code used by S/PDIF and TOSLINK. A self-clocking encoding where each bit period contains a transition at the start, with an additional mid-period transition representing a 1. Allows the receiver to recover the clock from the data stream without a separate clock signal.

**DSP — Digital Signal Processor**
In the audio context, a processor performing real-time mathematical operations on audio samples. In Simply TV, the TAS5805M speaker amplifier contains an integrated audio DSP implementing equalization, dynamic range compression, bass/treble control, and speaker protection. Configured via I2C register writes from the main processor.

**DTS — Digital Theater Systems**
A compressed multi-channel audio format competing with Dolby Digital. Used on DVD, Blu-ray, and some broadcast sources. Simply TV passes DTS bitstreams through to TOSLINK and eARC outputs and decodes to PCM stereo for internal speakers.

**I2S — Inter-IC Sound**
A simple 3-wire serial bus standard for digital audio between integrated circuits. Carries a bit clock, a word select signal (distinguishing left and right channels), and serial audio data. The universal internal audio interconnect in Simply TV — all audio ICs (ADC, DAC, amplifier) communicate over I2S.

**LPCM — Linear Pulse Code Modulation**
Uncompressed digital audio. Each sample is a direct numeric representation of the audio waveform amplitude. Standard CD audio is 16-bit/44.1kHz LPCM. Simply TV outputs LPCM over TOSLINK for all analog sources and HDMI sources carrying uncompressed audio.

**PCM — Pulse Code Modulation**
The general term for digitally sampled audio. LPCM is the most common form. In common usage, "PCM" on an audio output means uncompressed stereo audio, as distinct from Dolby or DTS compressed formats.

**PWM — Pulse Width Modulation**
A technique for controlling analog values using a digital signal by varying the ratio of on-time to off-time. In Simply TV, PWM is used for LED backlight brightness control (LED/LCD fallback path). PWM frequency affects flicker perception — Simply TV specifies a minimum of 960Hz to reduce perceptible flicker.

**SNR — Signal-to-Noise Ratio**
A measure of audio quality expressing the ratio of desired signal to background noise, in decibels. Higher is better. The PCM1802 ADC specifies 105dB SNR. The TAS5805M amplifier specifies 100dB SNR.

**THD+N — Total Harmonic Distortion plus Noise**
A measure of audio amplifier quality expressing distortion and noise as a percentage of the output signal. Lower is better. The TAS5805M specifies 0.04% THD+N at rated power.

**TrueHD — Dolby TrueHD**
Dolby's lossless audio codec used on Blu-ray and some streaming content. Carries the full Dolby Atmos object-based audio track. Cannot be carried over TOSLINK. Requires eARC for passthrough from TV to soundbar or AV receiver.

---

## RF and Broadcast

**8-VSB — 8-level Vestigial Sideband**
The modulation scheme used by ATSC 1.0 digital broadcast in the United States. Encodes digital data on an analog carrier using 8 amplitude levels. Robust for fixed antenna reception; less tolerant of multipath than ATSC 3.0's OFDM modulation.

**A3SA — ATSC 3.0 Security Authority**
The DRM and content protection framework for ATSC 3.0 broadcasts. Allows broadcasters to encrypt their ATSC 3.0 signals and restrict reception to certified devices. Simply TV explicitly does not implement A3SA. Unencrypted ATSC 3.0 content and all ATSC 1.0 content are fully supported.

**ACR — Automatic Content Recognition**
Technology used by smart TVs to identify what content is being displayed by periodically capturing frames and comparing them against a database. The primary surveillance mechanism in modern smart TVs. Simply TV contains no ACR capability of any kind.

**AGC — Automatic Gain Control**
A feedback circuit that automatically adjusts the amplification of a signal to maintain a consistent output level regardless of input level variation. Used in the RF tuner (Si2158A) to handle varying signal strengths from weak to strong broadcast stations. Also used in the ADV7183B analog video decoder for varying source levels.

**ATSC — Advanced Television Systems Committee**
The standards body that defines US digital television broadcast standards. ATSC 1.0 is the current standard in use since the 2009 digital transition. ATSC 3.0 (NextGen TV) is the newer voluntary standard with 4K, HDR, and IP-layer capability.

**ATSC 1.0**
The US digital television broadcast standard using 8-VSB modulation and MPEG-2 video encoding. In use since 2009. Required to be supported by all US television tuners. Carries PSIP program guide data. FCC requires broadcasters to simulcast ATSC 1.0 alongside ATSC 3.0 through at least 2027.

**ATSC 3.0 — NextGen TV**
The newer US broadcast standard using OFDM modulation, HEVC video encoding, and an IP transport layer. Supports 4K HDR, Dolby Atmos, and datacasting. Voluntary adoption by broadcasters. Introduces DRM capability (A3SA) which Simply TV does not implement.

**FEC — Forward Error Correction**
A technique where redundant data is added to a transmitted signal so that errors introduced during transmission can be detected and corrected at the receiver without retransmission. ATSC 1.0 uses Reed-Solomon FEC. ATSC 3.0 uses LDPC (Low-Density Parity Check) codes, which are more powerful and computationally intensive.

**IF — Intermediate Frequency**
In a superheterodyne radio receiver, the frequency to which an incoming RF signal is downconverted before demodulation. The Si2158A RF tuner downconverts the selected broadcast channel to a 5 MHz intermediate frequency for processing by the SL3000 demodulator.

**LDPC — Low-Density Parity Check**
A powerful forward error correction code used in ATSC 3.0 demodulation. More computationally intensive than ATSC 1.0's Reed-Solomon FEC but provides significantly better error correction, enabling reception at lower signal levels.

**LNA — Low Noise Amplifier**
The first amplification stage in the RF tuner. Amplifies the extremely weak antenna signal while adding minimal noise. The noise figure of the LNA largely determines the tuner's sensitivity — how weak a signal it can successfully receive. Integrated inside the Si2158A.

**MPEG-2**
The video compression standard used by ATSC 1.0 broadcast and standard-definition DVD. Significantly less efficient than modern codecs (HEVC, AV1) but universally supported. Simply TV decodes MPEG-2 video in the main processor for OTA broadcast.

**MPEG-TS — MPEG Transport Stream**
The packet-based container format used to carry digital broadcast content. Fixed-size 188-byte packets carrying multiplexed audio, video, and data for multiple channels. The output format of the SL3000 demodulator, received by the main processor for demuxing and decoding.

**NTSC — National Television Standards Committee**
The analog color television standard used in North America, Japan, and parts of South America. 525 lines, 30 frames per second (actually 29.97). Autodetected by the ADV7183B.

**OFDM — Orthogonal Frequency-Division Multiplexing**
The modulation scheme used by ATSC 3.0. Encodes data across many closely-spaced subcarriers simultaneously. More robust against multipath interference than 8-VSB, improving reception in urban and mobile environments.

**PAL — Phase Alternating Line**
The analog color television standard used in most of Europe, Australia, and parts of Asia and South America. 625 lines, 25 frames per second. Autodetected by the ADV7183B.

**QAM — Quadrature Amplitude Modulation**
The modulation scheme used by cable television systems. Clear QAM (unencrypted cable) can be received by a cable-capable tuner without a CableCARD. Simply TV's tuner specification includes optional clear QAM support for unencrypted cable channels.

**SECAM — Séquentiel Couleur à Mémoire**
The analog color television standard used in France and French-influenced countries, Russia, and parts of Africa and the Middle East. Autodetected by the ADV7183B.

**SDR — Software-Defined Radio**
A radio receiver architecture where signal processing traditionally done in dedicated analog hardware is instead performed in software on a programmable processor. The Saankhya SL3000 demodulator is based on an SDR platform, enabling it to support multiple broadcast standards (ATSC, DVB-T2, ISDB-T) via firmware.

---

## Hardware and Interfaces

**ADC — Analog-to-Digital Converter**
A circuit that converts a continuous analog signal into discrete digital samples. In Simply TV: the PCM1802 is the audio ADC converting analog RCA audio to I2S; the ADV7183B contains an internal 10-bit video ADC converting analog video to digital.

**BOM — Bill of Materials**
A structured list of all components required to build a hardware assembly, including part numbers, quantities, descriptions, and pricing. Simply TV maintains BOMs for all four implementation paths in `hardware/bom/`.

**DAC — Digital-to-Analog Converter**
A circuit that converts digital samples back to a continuous analog signal. In Simply TV: the PCM5102A is the headphone output DAC converting I2S audio to analog for the headphone amplifier.

**DDR — Double Data Rate**
A type of DRAM (dynamic random-access memory) that transfers data on both the rising and falling edges of the clock signal, doubling effective bandwidth. Used as frame buffer memory in scaler SoC designs. LPDDR4 is used in the MediaTek and CM4 options.

**ESD — Electrostatic Discharge**
A sudden flow of electricity between two electrically charged objects. Can damage or destroy electronic components. All external connectors in Simply TV (HDMI, RCA, F-connector, USB, headphone jack) include ESD protection components between the connector and the first active circuit.

**FFC — Flat Flexible Cable**
A thin, flat cable with multiple parallel conductors used for board-to-board connections inside consumer electronics. The Vx1 interface between the main board and the TCON board uses an FFC with controlled 100Ω differential impedance.

**FPGA — Field-Programmable Gate Array**
An integrated circuit containing a large array of programmable logic blocks that can be configured after manufacture to implement custom digital circuits. Simply TV's Option D uses a Lattice ECP5 FPGA to implement the video scaler pipeline in RTL hardware description language.

**GPIO — General-Purpose Input/Output**
A digital signal pin on a microcontroller or SoC that can be configured as either an input or output under software control. Used in Simply TV for panel power sequencing, IR receiver input, headphone jack insertion detection, and OLED refresh cycle triggering.

**I2C — Inter-Integrated Circuit**
A two-wire serial communication protocol (data + clock) used for low-speed communication between a controller and peripheral devices. Used extensively in Simply TV: controls the ADV7183B, PCM1802, TAS5805M, TPA6132A2, Si2158A tuner, TPS65161 PMIC, and SL3000 demodulator. Standard speeds are 100kHz (standard mode) and 400kHz (fast mode).

**MCU — Microcontroller Unit**
A small computer on a single integrated circuit containing a processor, memory, and programmable I/O peripherals. Simply TV's Option D uses an STM32H7 MCU as the main control processor alongside the FPGA.

**ODM — Original Design Manufacturer**
A company that designs and manufactures products that other companies brand and sell as their own. Simply TV's Option A and B paths require working with an ODM that has existing relationships with panel suppliers and chip vendors and access to proprietary SDKs.

**OEM — Original Equipment Manufacturer**
A company that makes components or products used in another company's end product. In the TV industry, ODM and OEM terms are often used interchangeably.

**PCB — Printed Circuit Board**
The physical board on which electronic components are mounted and interconnected by copper traces. Layer count affects signal integrity and routing density. Simply TV's main board requires 6–10 layers depending on the implementation path.

**PMIC — Power Management Integrated Circuit**
An IC that integrates multiple power supply functions — voltage regulators, power sequencing, supervisory circuits — in a single package. Simply TV uses the TI TPS65161 as the LCD/OLED panel PMIC, generating VGH, VGL, VCOM, and AVDD from the primary supply.

**RTL — Register-Transfer Level**
A design abstraction for digital circuits describing the flow of signals between registers and the logical operations performed on those signals. Written in hardware description languages such as VHDL or Verilog. Simply TV's Option D FPGA scaler is implemented in RTL.

**SoC — System on a Chip**
An integrated circuit that combines most components of a computer system — processor cores, memory controllers, I/O interfaces, and often domain-specific accelerators — on a single die. The Realtek RTD2795 and MediaTek MT5895 are display-oriented SoCs that integrate the entire TV signal processing pipeline.

**SPI — Serial Peripheral Interface**
A four-wire synchronous serial communication interface (clock, MOSI, MISO, chip select) used for high-speed communication between a controller and peripheral devices. Used in Options A and B to connect the main SoC to external NOR flash memory storing the firmware image.

**TVS — Transient Voltage Suppressor**
A semiconductor device designed to clamp voltage spikes and transients to protect downstream circuitry. Used at all external connectors in Simply TV. Must be chosen with sufficiently low capacitance that it does not attenuate high-frequency signals — critical for RF inputs where even 1pF of additional capacitance affects impedance matching.

**UART — Universal Asynchronous Receiver-Transmitter**
A serial communication interface for asynchronous data transmission. Used in Simply TV for debug console output during firmware development. The kernel console is redirected to UART in production builds so that boot messages do not appear on the main display.

---

## Firmware and Software

**A/B Partitioning**
A firmware update strategy using two storage partitions for the operating system. One partition is active (running); the other is standby. Updates are written to the standby partition. If the update boots successfully, the standby becomes the new active. If it fails to boot after a configurable number of attempts, the system reverts to the previous active partition. Simply TV uses A/B partitioning on the CM4's eMMC for safe firmware updates.

**DRM — Digital Rights Management**
In the software context (distinct from Linux DRM/KMS), a system of access controls applied to digital content to restrict its use. ATSC 3.0's A3SA system is a DRM framework. Simply TV does not implement any DRM system for content reception. The term is also used in Linux kernel display management context (Direct Rendering Manager) — context determines meaning.

**eMMC — Embedded MultiMediaCard**
A flash storage standard integrated directly into the system board or module. The Raspberry Pi CM4 includes eMMC storage on the module itself. Used for the operating system, firmware, and settings partition in Simply TV's CM4 implementation.

**evdev — Event Device**
The Linux kernel input subsystem that presents input devices (keyboards, mice, remote controls, IR receivers) as event files under `/dev/input/`. Simply TV's CM4 firmware reads IR remote control events from an evdev device, avoiding the complexity of the legacy LIRC subsystem.

**GStreamer**
An open source multimedia pipeline framework for Linux. Applications construct processing pipelines by linking modular elements (sources, filters, sinks). Simply TV's CM4 firmware uses GStreamer to manage all video input paths: capturing from V4L2 devices, format conversion, and output to the DRM display plane.

**HEVC — High Efficiency Video Coding (H.265)**
A video compression standard approximately twice as efficient as its predecessor H.264. Used for ATSC 3.0 broadcast and 4K streaming content. The Raspberry Pi CM4's VideoCore VI includes hardware HEVC decode capability, avoiding the need for CPU-intensive software decoding.

**initramfs — Initial RAM Filesystem**
A temporary root filesystem loaded into RAM during Linux boot, before the real root filesystem is mounted. Used in Simply TV's CM4 fast-boot configuration to initialize the display and start the TV application before the full filesystem is available, reducing perceptible boot time.

**LIRC — Linux Infrared Remote Control**
A Linux subsystem for interfacing with IR receivers and transmitters. The traditional approach for IR remote control on Linux. Simply TV uses the newer evdev approach instead, which integrates more cleanly with the Linux input event system.

**NOR Flash**
A type of non-volatile flash memory with random read access and block-erase write capability. Used in Options A and B to store the scaler firmware image. Accessed via SPI interface. Typically 8–32MB capacity.

**RTOS — Real-Time Operating System**
An operating system designed to process events or data within strict timing constraints. Characterized by deterministic scheduling and minimal latency. FreeRTOS and Zephyr are examples. Relevant to Simply TV's Option D (ARM+FPGA) path; the CM4 path uses Linux rather than an RTOS.

**systemd**
The init system and service manager used by most modern Linux distributions including Raspberry Pi OS. Manages the boot process and controls which services start in what order. Simply TV's CM4 build strips systemd down to the minimum required services, eliminating network management, Bluetooth, and other unnecessary components to achieve fast boot times.

**V4L2 — Video4Linux version 2**
The Linux kernel API for video capture and output devices. Video capture hardware (including the TC358743 HDMI bridge in Simply TV's CM4 path) presents itself as V4L2 devices. GStreamer reads from V4L2 devices using the `v4l2src` element.

**vc4 / vc4-kms-v3d**
The Linux kernel driver for the Broadcom VideoCore IV/VI GPU used in Raspberry Pi devices. Implements the DRM/KMS interface for display output and provides OpenGL ES support. Simply TV's CM4 firmware uses the vc4 driver for DRM plane management and display output.

---

## Certification and Compliance

**CE Marking**
A mandatory conformity marking for products sold in the European Economic Area. Indicates the product meets EU safety, health, and environmental requirements. Not validated in Simply TV v1 (US-focused) but documented in `docs/CERTIFICATION.md` for reference.

**CISPR — Comité International Spécial des Perturbations Radioélectriques**
The international standards body for electromagnetic interference. CISPR standards underpin FCC and CE emissions requirements.

**FCC — Federal Communications Commission**
The US regulatory agency governing communications by radio, television, wire, satellite, and cable. FCC Part 15 certification is required for any electronic device sold in the US, covering unintentional radio frequency emissions. Simply TV requires FCC Part 15 certification before commercial sale.

**HDMI LLC**
The licensing administrator for the HDMI specification. Any product incorporating HDMI must license from HDMI LLC, pay annual fees, and pay per-unit royalties. Required for Simply TV on all implementation paths that include HDMI.

**UL — Underwriters Laboratories**
A US safety certification organization. UL or equivalent ETL listing is required for electrical products sold in the US, covering electrical safety (shock, fire hazard). Simply TV requires UL or ETL safety listing before commercial sale.
