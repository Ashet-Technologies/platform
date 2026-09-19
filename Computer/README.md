# Ashet Home Computer

This directory documents the concrete Ashet Home Computer design built on top of the interfaces defined in [Platform](../Platform/).

The Platform documentation is authoritative for the interfaces between Mainboard, Backplane and Expansion Cards. This document describes the current computer implementation and deliberately does not redefine those interfaces.

## Architecture

The computer uses a modular architecture made of independently replaceable functional units:

```
Mainboard
   │
   │ Mainboard interface
   ▼
Backplane / Southbridge
   │
   │ Expansion interface
   ▼
Expansion Cards
```

The current computer has seven Expansion Card slots:

- 5 Generic Expansion slots
- 1 Generic + Video Expansion slot
- 1 Generic + Audio Expansion slot

The power supply is also a replaceable unit.

## Mainboard

The Mainboard contains the CPU, system memory and non-volatile storage and runs Ashet OS.

The current Mainboard provides:

- Raspberry Pi RP2350 main SoC
  - 150 MHz
  - two CPU cores
  - Arm Cortex-M33 or RISC-V cores
  - 512 KiB internal SRAM
- 8 MB PSRAM
- 16 MB Flash
- USB 1.1 Host
- 10/100 Mbps Ethernet
- Battery-backed real-time clock
- Integrated debug probe

The debug probe provides hardware debugging and a high-speed UART interface for logging and remote control.

## Backplane

The Backplane interconnects the Mainboard and all Expansion Cards.

The current Backplane contains the Propeller 2 Southbridge. The Southbridge dispatches data between the Mainboard and Expansion Cards and provides the main electrical expansion interface.

The exact electrical interface and connector pinout are defined by the [Platform documentation](../Platform/).

## Expansion Cards

Expansion Cards provide user-selectable features. The designated card set for the Ashet Home Computer consists of:

### Framebuffer Video Card

- DVI video output
- default mode: 640×400
- 8 bits per pixel
- 60 Hz

### PCM Sound Card

- PCM audio output
- PCM audio input
- 48 kHz
- 16 bit

### USB Card

- 4 USB 1.1 Host ports

### RS232 Card

- UART
- 3.3 V, 5 V and ±12 V electrical interfaces

### Basic I/O Card

- pin-header connector
- 8 GPIOs
- dedicated I²C
- 5 V and 3.3 V power

### Commodore Connectivity Card

- 2× C64 Serial interfaces

### User Expansion Card

- minimal Expansion Card implementation
- perfboard area for custom circuitry
- pin-header connector

## Mechanical Design

The current computer design uses a stock ABS enclosure with approximate external dimensions of 100 mm × 180 mm × 250 mm.

The rear panel provides a mechanical power switch and a 12 V barrel-jack power input.

## Video

The default video mode is **640×400 at 8 bpp and 60 Hz**.

The rationale for this resolution is documented in [Decisions/0001-default-video-resolution.md](Decisions/0001-default-video-resolution.md).
