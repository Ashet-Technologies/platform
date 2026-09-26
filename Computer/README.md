# Ashet Home Computer

This directory documents the concrete Ashet Home Computer design built on top of the interfaces defined in [Platform](../Platform/).

The Platform documentation is authoritative for the interfaces between Mainboards, the Backplane, Slots, and Expansion Cards. This document describes the first concrete computer implementation and deliberately does not redefine those interfaces.

## Architecture

The computer uses a modular architecture made of independently replaceable functional units:

```
Default Mainboard
   │
   │ Mainboard interface
   ▼
Backplane / Southbridge
   │
   ├── 7 Expansion Slots
   │
   └── Power Board
```

The system contains exactly one Mainboard Slot and seven Expansion Slots:

- 5 Generic Expansion Slots
- 1 Generic + High-Speed Expansion Slot
- 1 Generic + Audio Expansion Slot

## Default Mainboard

The first fully realized Mainboard design is called the **Default Mainboard**. It is the reference implementation intended to be built first, not the only Mainboard concept planned for the computer.

The Default Mainboard provides:

- RP2350B system CPU
  - 150 MHz
  - two CPU cores
  - Arm Cortex-M33 or RISC-V
  - 512 KiB internal SRAM
- 8 MiB APS6404L-3SQR-SN QSPI PSRAM
- 16 MiB Flash
- ENC624J600 10/100 Ethernet controller
- four-port USB host hub
  - USB0 to the Backplane
  - USB1 to the Backplane
  - one internal USB-A connector, primarily for USB mass storage
  - one front-panel USB-A connector
- second RP2350B running modified Picoprobe firmware as an integrated debug/flash probe

The integrated debug probe provides in-system flashing, hardware debugging, and a high-speed UART interface for logging and remote control.

USB Mass Storage Class is the primary intended in-system mass-storage interface. Standard USB flash drives can be used directly, and USB-to-SD adapters allow use of SD cards without a native SD interface.

The eight Platform HSTX lanes are routed to HSTX-capable RP2350 pins. Those pins may use the RP2350 HSTX peripheral, PIO, or other applicable peripherals depending on the selected HSTX interface.

For the Default Mainboard, the HSTX lanes have up to 300 MHz transmit capability and up to 150 MHz receive capability.

## Backplane

The Backplane contains the Propeller 2 Southbridge and interconnects the Mainboard Slot and seven Expansion Slots.

The Backplane uses one CH32V003 board-management controller per Expansion Slot. Expansion Slots 0 through 6 use I²C addresses `0x20` through `0x26`, respectively. A Backplane board-management controller uses `0x27`.

The Backplane also contains a battery-backed **PCF8523** real-time clock at I²C address `0x68` and a Backplane configuration EEPROM at `0x57`.

The Backplane does not contain the system power regulators. Instead, a separate internal **Power Board** connects to the Backplane and generates the required rails.

This separates development of the power supply from the Backplane logic and permits replaceable power implementations.

## Default Power Supply

The default configuration uses a ready-made external 12 V wall-wart power adapter connected through a barrel jack.

The internal Power Board derives the required system rails from this 12 V input.

Alternative Power Boards may use other sources, including integrated mains power, batteries, or USB-C.

## Expansion Cards

Expansion Cards provide user-selectable features. The designated card set for the Ashet Home Computer consists of:

### Framebuffer Video Card

- DVI video output
- default mode: 640×400
- 8 bpp [Ashet HSV](../Platform/Ashet%20HSV.md)
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

The computer design uses a stock ABS enclosure with approximate external dimensions of 100 mm × 180 mm × 250 mm.

The rear panel provides a mechanical power switch and a 12 V barrel-jack power input.

Each Expansion Slot includes guard rails that guide an Expansion Card into its PCI Express x4 connector. The rails align the card before it reaches the approximately 2 mm connector slot, avoiding difficult blind alignment during insertion.

Expansion Cards are installed without retention screws. The PCI Express x4 connector and Expansion Slot guide rails provide sufficient retention, and omitting the screw hardware frees 10 mm of vertical card-slot space for connectors.

## Video

The default video mode is **640×400 at 8 bpp and 60 Hz**.

The rationale for this resolution is documented in [Decisions/0001-default-video-resolution.md](Decisions/0001-default-video-resolution.md).
