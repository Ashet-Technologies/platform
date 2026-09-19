# Ashet Expansion Bus

The Ashet *Expansion Bus* is the core component of the composability of the Ashet Home Computer.

## Overview

- Hot Swappable
- Power Supply
  - 12 V, 500 mA
  - 5 V, 500 mA
  - 3.3 V, 500 mA
  - Overcurrent Protection
- I²C Bus, up to 400 kHz
- 8 Programmable I/O Pins, up to 300 MHz
- 8 High Speed Lanes, up to 300 MHz
- Dual-Stream I²S Bus

## Optional Features

The Expansion Bus has several *optional* features that must not be present on each slot.

Each feature might require a certain set of signals to be present:

| Feature  | Standard Signals | Video Signals | Audio Signals |
| -------- | ---------------- | ------------- | ------------- |
| Standard | ✅                | ❌             | ❌             |
| Audio    | ✅                | ❌             | ✅             |
| Video    | ✅                | ✅             | ❌             |

In addition, `/SLOT_FUNC0` and `/SLOT_FUNC1` indicate unspecified slot-specific features. A feature signal is connected to GND when that feature is available.

## Signals

### Power and Configuration

| Signal Name     | Driver    | Type   | Level | Count | Function                                                                    | Frequency Limit |
| --------------- | --------- | ------ | ----- | ----: | --------------------------------------------------------------------------- | --------------- |
| `GND`           | Backplane | Power  | 0 V   |    20 | Signal Ground                                                               | -               |
| `+3V3`          | Backplane | Power  | 3.3 V |     2 | 3.3 V, 500 mA Power Supply                                                  | -               |
| `+5V`           | Backplane | Power  | 5 V   |     2 | 5 V, 500 mA Power Supply                                                    | -               |
| `+12V`          | Backplane | Power  | 12 V  |     2 | 12 V, 500 mA Power Supply                                                   | -               |
| `/PRESENT`      | Card      | Static | 0 V   |     1 | Presence detection. Must be tied to ground on the expansion card            | -               |
| `/SLOT_AUDIO`   | Backplane | Static | 0 V   |     1 | Connected to GND if the slot has the *Audio* feature available               | -               |
| `/SLOT_VIDEO`   | Backplane | Static | 0 V   |     1 | Connected to GND if the slot has the *Video* feature available               | -               |
| `/SLOT_FUNCx`   | Backplane | Static | 0 V   |     2 | Connected to GND if the slot has an unspecified feature available            | -               |

### Standard Signals

| Signal Name    | Driver    | Type                           | Level | Count | Function                                         | Frequency Limit |
| -------------- | --------- | ------------------------------ | ----- | ----: | ------------------------------------------------ | --------------- |
| `/RESET`       | Backplane | Logic                          | 3.3 V |     1 | Reset signal. Driven low when the card should reset itself | 1 kHz     |
| `CLK`          | Backplane | Logic                          | 3.3 V |     1 | Global 48 MHz clock for synchronization          | 8 MHz           |
| `I2C_SCL`      | Bi-di     | Open Collector                 | 3.3 V |     1 | Clock lane of the System I²C Bus                 | 400 kHz         |
| `I2C_SDA`      | Bi-di     | Open Collector                 | 3.3 V |     1 | Data lane of the System I²C Bus                  | 400 kHz         |
| `GP0`…`GP7`  | Bi-di     | Logic, Differential or Analog  | 3.3 V |     8 | General-purpose I/O signals from the Southbridge | 300 MHz         |

### Video Signals

| Signal Name       | Driver | Type                  | Level | Count | Function                                                               | Frequency Limit |
| ----------------- | ------ | --------------------- | ----- | ----: | ---------------------------------------------------------------------- | --------------- |
| `HSTX0`…`HSTX7` | Bi-di  | Logic or Differential | 3.3 V |     8 | Uni-directional high-speed lanes. Even/odd pairs for a differential pair | 300 MHz       |

### Audio Signals

| Signal Name | Driver    | Type  | Level | Count | Function                              | Frequency Limit |
| ----------- | --------- | ----- | ----- | ----: | ------------------------------------- | --------------- |
| `I2S_MCLK`  | Backplane | Logic | 3.3 V |     1 | Master clock of both I²S streams      | 25 MHz          |
| `I2S_BCLK`  | Backplane | Logic | 3.3 V |     1 | Bit clock of both I²S streams         | 6.5 MHz         |
| `I2S_WCLK`  | Backplane | Logic | 3.3 V |     1 | Word clock of both I²S streams        | 192 kHz         |
| `I2S_SDIN`  | Card      | Logic | 3.3 V |     1 | Data lane of the I²S input stream     | 6.5 MHz         |
| `I2S_SDOUT` | Backplane | Logic | 3.3 V |     1 | Data lane of the I²S output stream    | 6.5 MHz         |

### Reserved Signals

| Signal Name | Driver | Count | Function                                        |
| ----------- | ------ | ----: | ----------------------------------------------- |
| `RESERVEDx` | N.C.   |     8 | Reserved for future use. Do not connect to anything. |

All signals that are not power signals use nominal voltage levels between 0.0 V and 3.3 V.

> **Sheet inconsistencies:** The current source sheet describes `CLK` as a “Global 48 MHz clock” while listing its frequency limit as 8 MHz. It also marks the `HSTX` driver as “Bi-di” while describing the lanes as uni-directional. Both values are preserved here pending resolution.

### Power

The current signal specification defines 3.3 V, 5 V and 12 V supplies at 500 mA each. Further electrical requirements such as tolerances, sequencing and inrush limits are not specified by the source sheet.

### I²C

The I²C bus is specified for operation up to 400 kHz and must have at least a standard EEPROM connected.

This EEPROM must have at an 8-bit memory organization with at least 8K of memory. It contains the *Module Descriptor Data* described further below.

The following addresses are reserved on the bus in addition to the specification:

| Address | Use                  |
| ------: | -------------------- |
|    0x57 | Metadata EEPROM      |
|    0x70 | Backplane I²C Switch |

All other addresses on the I²C bus are available to the expansion card and will not be occupied by the host system.

> **LORE:**
> The *Metadata EEPROM* uses the address `0x57` instead of `0x50`, as `0x50` is the default for EEPROMs and these might already be taken by other
> EEPROM systems like the [DDC](https://en.wikipedia.org/wiki/Display_Data_Channel) [EDID](https://en.wikipedia.org/wiki/Extended_Display_Identification_Data) EEPROM.

### General Purpose I/O

These signals will have a card-specific function and are driven by either the card or the southbridge on the backplane.

They can be logic, differential or analog signals, as long as they stay in the nominal voltage range.

Each expansion card will have to ship a low level driver which provides the southbridge configuration for these I/Os. See *Module Descriptor Data* for more information.

### I²S

The I²S bus has two audio streams on the signals `I2S_SDIN` and `I2S_SDOUT`.

`I2S_SDOUT` contains the left and right channel data of a stereo audio output, while `I2S_SDIN` contains the left and right channel data of a stereo capturing device.

The clock signals are shared for both audio streams and are always driven by the host system. This means that the sample rate is defined by the host and cannot be set by the card itself.

Cards that need to be in control of their sample rate might need to do audio resampling or use the *General Purpose I/O* signals.

It is only available on slots with the *Audio* signal set.

### High Speed Lanes

> TO BE DONE

The high speed lanes are only available on slots with the *Video* signal set.

## Connector

### Mechanical

The *Expansion Bus* uses a standard *PCI Express x4* connector with 64 positions.

### Pinout

This table is the expansion-card-facing pinout from the current *Pin Out* sheet. See [Pinout.md](Pinout.md) for both mainboard-facing and expansion-card-facing mappings.

| Pin | A Side       | B Side        |
| --: | ------------ | ------------- |
|   1 | GND          | GND           |
|   2 | +12V         | +12V          |
|   3 | +5V          | +5V           |
|   4 | +3V3         | +3V3          |
|   5 | GND          | GND           |
|   6 | I2C_SCL      | /SLOT_AUDIO   |
|   7 | I2C_SDA      | /SLOT_VIDEO   |
|   8 | +3V3 10K     | /SLOT_FUNC0   |
|   9 | /RESET       | /SLOT_FUNC1   |
|  10 | GND          | /PRESENT      |
|  11 | CLK          | GND           |
|  12 | GND          | GND           |
|  13 | HSTX0        | GP0           |
|  14 | HSTX1        | GP1           |
|  15 | GND          | GND           |
|  16 | HSTX2        | GP2           |
|  17 | HSTX3        | GP3           |
|  18 | GND          | GND           |
|  19 | HSTX4        | GP4           |
|  20 | HSTX5        | GP5           |
|  21 | GND          | GND           |
|  22 | HSTX6        | GP6           |
|  23 | HSTX7        | GP7           |
|  24 | GND          | GND           |
|  25 | I2S_SDIN     | RESERVED0     |
|  26 | I2S_SDOUT    | RESERVED1     |
|  27 | GND          | RESERVED2     |
|  28 | I2S_BCLK     | RESERVED3     |
|  29 | I2S_WCLK     | RESERVED4     |
|  30 | GND          | RESERVED5     |
|  31 | I2S_MCLK     | RESERVED6     |
|  32 | GND          | GND           |

The source sheet labels A8 as `+3V3 10K`. It names only `RESERVED0` through `RESERVED6` in the expansion-card-facing pinout even though the signal specification lists eight reserved signals; this document does not infer a missing `RESERVED7`.

## Module Descriptor Data

The Module Descriptor Data describes the expansion card and provides a low-level driver for the southbridge.

### Memory Map

The data is located at the start of the EEPROM and follows the following memory layout:

| Address Range  | Function                |
| -------------- | ----------------------- |
| `0000`..`01FF` | Metadata Block          |
| `0200`..`07FF` | *reserved*              |
| `0800`..`0FFF` | Module Card Icon        |
| `1000`..`1FFF` | Low Level Driver Binary |

### Metadata Block

The metadata block encodes generic information about the expansion card that can be processed by the host system.

| Offset | Field             | Type     | Function                                                |
| ------ | ----------------- | -------- | ------------------------------------------------------- |
| `0000` | Vendor ID         | `u32`    | Unique ID for the vendor of the expansion card          |
| `0004` | Product ID        | `u32`    | Vendor-unique ID for the expansion card                 |
| `0008` | Serial Number     | `[8]u8`  | Serial number of the expansion card. Can be zero-padded |
| `0018` |                   |          | *padding*                                               |
| `0020` | Required Features | `u8`     | Bitmask of which expansion slot features are required   |
| `0021` |                   |          | *padding*                                               |
| `0024` | Driver Interface  | `u32`    | Type of driver interface this expansion card uses       |
| `0028` |                   |          | *padding*                                               |
| `0030` | Driver Specific   | `[16]u8` |                                                         |
| `0040` |                   |          | *padding*                                               |
| `0100` | Vendor Name       | `[64]u8` | UTF-8 encoded vendor name                               |
| `0140` | Product Name      | `[64]u8` | UTF-8 encoded product name                              |
| `0180` |                   |          | *padding*                                               |

### Module Card Icon

Each module card may embed an icon so a host os can show the user a nice visual representation.

As the resolution of the host system is not known, up to three icon sizes can be embedded:

- 16x16
- 24x24
- 32x32

All icons share the same color palette, which can have up to 63 colors and a transparency key.

The icon memory block is organized as such:

| Address Range  | Function                                    |
| -------------- | ------------------------------------------- |
| `0000`..`00FF` | 8-bit pixel data for 16x16 icon             |
| `0100`..`04FF` | 8-bit pixel data for 32x32 icon             |
| `0500`..`0BFF` | 8-bit pixel data for 24x24 icon             |
| `0740`         | 16x16 icon Configuration Field              |
| `0741`         | 32x32 icon Configuration Field              |
| `0742`         | 24x24 icon Configuration Field              |
| `0743`..`07FF` | 63-entry palette with \[R,G,B] color values |

Each *Configuration Field* is a bit field with the following items:

|  Bit | Function                                               |
| ---: | ------------------------------------------------------ |
| 0..5 | Number of palette entries. Zero means icon is disabled |
| 6..7 | *reserved, must be zero*                               |

An icon is present if it has more than zero colors in its palette.

The pixel data contains row-major organized indices into the palette using 0...62 as defined by the palette table, and 63 as the transparency key. On load, the uppermost two bits may be discarded, truncating the 8-bit index to 6 bits.

### Low Level Driver

The low level driver is a Propeller 2 binary which is loaded into one of the cogs of the southbridge.

> TO BE DONE
