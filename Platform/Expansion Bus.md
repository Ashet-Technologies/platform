# Ashet Expansion Bus

The Ashet *Expansion Bus* is the core component of the composability of the Ashet Home Computer.

## Overview

- Optional hot-swap support
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

In addition, `/SLOT_FUNC0` and `/SLOT_FUNC1` indicate unspecified slot-specific features.

The slot feature straps `/SLOT_AUDIO`, `/SLOT_VIDEO`, `/SLOT_FUNC0`, and `/SLOT_FUNC1` are passive Backplane signals:

- a supported feature is indicated by a hard 0 Ω connection to GND
- an unsupported feature is left unconnected
- strap state must not depend on slot power
- a card that needs a defined level for an unconnected strap should provide its own pull-up

A card may sample these straps at any time.

## Signals

Signal names prefixed with `/` are active-low.

Unless specified otherwise, signal voltages are referenced to GND and must remain within 0.0 V to 3.3 V.


### Power and Configuration

| Signal Name     | Driver    | Type   | Level | Count | Function                                                                    | Frequency Limit |
| --------------- | --------- | ------ | ----- | ----: | --------------------------------------------------------------------------- | --------------- |
| `GND`           | Backplane | Power  | 0 V   |    20 | Signal Ground                                                               | -               |
| `+3V3`          | Backplane | Power  | 3.3 V |     2 | 3.3 V, 500 mA Power Supply                                                  | -               |
| `+5V`           | Backplane | Power  | 5 V   |     2 | 5 V, 500 mA Power Supply                                                    | -               |
| `+12V`          | Backplane | Power  | 12 V  |     2 | 12 V, 500 mA Power Supply                                                   | -               |
| `/PRESENT`      | Card      | Static | 0 V   |     1 | Presence detection. Must be tied to GND through 0 Ω on the Expansion Card   | -               |
| `/SLOT_AUDIO`   | Backplane | Static | 0 V   |     1 | Connected to GND if the slot has the *Audio* feature available               | -               |
| `/SLOT_VIDEO`   | Backplane | Static | 0 V   |     1 | Connected to GND if the slot has the *Video* feature available               | -               |
| `/SLOT_FUNCx`   | Backplane | Static | 0 V   |     2 | Connected to GND if the slot has an unspecified feature available            | -               |

### Standard Signals

| Signal Name    | Driver    | Type                           | Level | Count | Function                                         | Frequency Limit |
| -------------- | --------- | ------------------------------ | ----- | ----: | ------------------------------------------------ | --------------- |
| `/RESET`       | Backplane | Open Drain                     | 3.3 V |     1 | Reset signal. Driven low when the card should reset itself | 1 kHz     |
| `CLK`          | Backplane | Logic                          | 3.3 V |     1 | Global 48 MHz clock for synchronization          | 48 MHz          |
| `I2C_SCL`      | Bi-di     | Open Collector                 | 3.3 V |     1 | Clock lane of the System I²C Bus                 | 400 kHz         |
| `I2C_SDA`      | Bi-di     | Open Collector                 | 3.3 V |     1 | Data lane of the System I²C Bus                  | 400 kHz         |
| `GP0`…`GP7`  | Bi-di     | Logic, Differential or Analog  | 3.3 V |     8 | General-purpose I/O signals from the Southbridge | 300 MHz         |

### Video Signals

| Signal Name       | Driver | Type                  | Level | Count | Function                                                               | Frequency Limit |
| ----------------- | ------ | --------------------- | ----- | ----: | ---------------------------------------------------------------------- | --------------- |
| `HSTX0`…`HSTX7` | Bi-di  | Logic or Differential | 3.3 V |     8 | Bidirectional high-speed lanes. Even/odd pairs for a differential pair | 300 MHz       |

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
| `RESERVEDx` | N.C.   |     7 | Reserved for future use. Do not connect to anything. |

All signals that are not power signals use nominal voltage levels between 0.0 V and 3.3 V.

### Power

The Expansion Bus defines 3.3 V, 5 V and 12 V supplies at 500 mA each.

The 500 mA limit applies to the complete rail for one slot and is shared across all connector contacts carrying that rail.

For a slot, the three rails are switched synchronously: 3.3 V, 5 V and 12 V are either all enabled or all disabled.

Each rail is protected by an eFuse. The specific eFuse implementation is outside the Platform interface.

An Expansion Card must not back-power an unpowered slot through a power rail or signal.

When slot power is disabled, card-facing signals should be high-impedance and the card should not rely on host-side bias being present.

Further electrical requirements such as rail tolerances, detailed sequencing, and inrush limits are not yet specified.

Host-side biasing of `/PRESENT` is not part of the Expansion Card electrical contract.

### Reset Behavior

`/RESET` is an active-low, 3.3 V open-drain signal.

A card only needs to provide a pull-up for `/RESET` when it uses the signal. Cards that do not use `/RESET` may leave it unconnected.

For every reset event, `/RESET` is asserted low for at least **50 ms**.

On slot power-on:

- `/RESET` is already asserted when the slot rails turn on
- `/RESET` is not released earlier than 50 ms after power-on

A card that uses `/RESET` must enter a deterministic safe state equivalent to its power-on reset state while reset is asserted.

A card must not create bus contention when leaving reset.

### Hot Swap

An implementation may support hot-swapping Expansion Cards, but hot-swap support is not required for platform compatibility. Hot-swap sequencing and electrical behavior are not yet specified.

### Slot Clock

`CLK` is a 48 MHz clock buffered per slot.

A slot may mute its clock by actively driving `CLK` to GND when the clock is not enabled for that slot.

When `CLK` is enabled for a card, it must be stable before `/RESET` is released.

The mechanism used to request per-slot clock enablement is not yet specified.

### I²C

Each Expansion Card slot provides its own I²C bus segment, specified for operation up to 400 kHz.

`I2C_SCL` and `I2C_SDA` use open-drain signaling in the 3.3 V domain. Pull-ups are provided by the Backplane and are present only while the slot is powered.

From the Expansion Card's point of view, this is a complete I²C bus. The platform reserves only the following addresses in addition to addresses reserved by the I²C specification itself:

| Address | Use                     |
| ------: | ----------------------- |
|    0x57 | Metadata EEPROM         |
|    0x77 | PCA9547 I²C multiplexer |

All other I²C addresses are available to the Expansion Card and will not be occupied by the platform.

Each Expansion Card must provide a metadata EEPROM at address `0x57`. The EEPROM must provide at least 4 KiB of storage. Cards that embed an icon block must use at least an 8 KiB EEPROM. The EEPROM contains the Expansion Card metadata and may contain a card-specific low-level driver.

The Backplane uses a PCA9547 to select the I²C bus segment belonging to a particular Expansion Card slot. Its control address is `0x77`.

The architectural rationale for the per-slot I²C topology and the selected reserved addresses is documented in [Decisions](Decisions/).

### General Purpose I/O

These signals have a card-specific function and may be driven by either the card or the Southbridge on the Backplane.

Each GP lane is backed directly by a Propeller 2 I/O pin and exposes the full Smart Pin capability of that pin, subject to the electrical limits of the Expansion Bus.

They may be used for logic, analog, PWM, differential signaling, and software-defined interfaces such as USB 1.1 while remaining inside the nominal voltage range.

The differential pairs are:

- `GP0/GP1`
- `GP2/GP3`
- `GP4/GP5`
- `GP6/GP7`

Each pair may be used as a differential input pair or as two independent single-ended signals. Differential input may use the Propeller 2 analog comparator with optional biasing and Schmitt-trigger behavior. Every pair is suitable for use as a USB 1.1 PHY pair.

When used as logic, the Low-Level-Driver may configure the Smart Pin input/output behavior and thresholds per pin.

PWM output is supported. Smart Pin DAC output may use the following drive modes:

| Output impedance | Full-scale voltage |
| ---: | ---: |
| 990 Ω | 3.3 V |
| 600 Ω | 2.0 V |
| 123.75 Ω | 3.3 V |
| 75 Ω | 2.0 V |

Before a slot's Low-Level-Driver is active, the Southbridge keeps its GP lanes high-impedance.

An Expansion Card should also leave GP lanes high-impedance until its interface is configured. A card that drives a GP lane before activation must do so in a way that remains non-destructive under possible contention.

An Expansion Card may ship a card-specific Low-Level-Driver that configures these I/Os, or use a platform-standard driver interface. See [Expansion Card EEPROM.md](Expansion%20Card%20EEPROM.md).

### Low-Level Drivers

The Southbridge resource partition and Low-Level-Driver execution model are specified in [Low Level Drivers.md](Low%20Level%20Drivers.md).

### I²S

The I²S bus has two audio streams on the signals `I2S_SDIN` and `I2S_SDOUT`.

`I2S_SDOUT` contains the left and right channel data of a stereo audio output, while `I2S_SDIN` contains the left and right channel data of a stereo capturing device.

The clock signals are shared for both audio streams and are always driven by the host system. This means that the sample rate is defined by the host and cannot be set by the card itself.

Cards that need to be in control of their sample rate might need to do audio resampling or use the *General Purpose I/O* signals.

It is only available on slots with the *Audio* signal set.

### High Speed Lanes

The high-speed lanes are only available on slots with the *Video* signal set.

HSTX provides a low-latency, high-bandwidth path directly between Mainboard and Expansion Card, bypassing the Southbridge.

The HSTX protocol is selected by the Expansion Card driver. The selected interface definition determines the direction and meaning of each HSTX pin.

Until an HSTX interface is enabled, both sides keep the HSTX pins high-impedance. A card must not drive HSTX pins until the selected interface permits it.

The Platform defines the following standard interface classes:

| Interface | Meaning |
| --- | --- |
| Unused | HSTX pins are unused and both sides keep them high-impedance |
| Custom | Expansion Card driver defines the complete HSTX behavior |
| DVI | Standard DVI HSTX interface |
| QSPI | Standard quad-SPI HSTX interface |
| QPI | Standard quad-I/O HSTX interface |
| MIPI-DSI (1 lane) | Standard one-lane MIPI-DSI HSTX interface |
| MIPI-DSI (2 lane) | Standard two-lane MIPI-DSI HSTX interface |
| MIPI-CSI | Standard MIPI-CSI HSTX interface |

The exact pin mappings and detailed electrical/protocol requirements of the standard HSTX interfaces are not yet specified.

### Activation Sequence

At interface level, slot activation proceeds in this order:

1. The slot power rails are enabled together.
2. `/RESET` is held low during power-up.
3. GP and HSTX lanes remain in their safe/default state until configured.
4. `/RESET` is released no earlier than 50 ms after slot power-on.

Further activation steps involving future clock/profile metadata are not defined here.

## Connector

### Mechanical

The *Expansion Bus* uses a standard *PCI Express x4* connector with 64 positions.

### Pinout

This table defines the Expansion Card-facing pinout. See [Pinout.md](Pinout.md) for both Mainboard-facing and Expansion Card-facing mappings.

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

Expansion-facing A8 is pulled up to `+3V3` through 10 kΩ. See [Pinout.md](Pinout.md) for its dual-function Mainboard/Expansion behavior.

## Expansion Card EEPROM

The Expansion Card EEPROM binary format, metadata block, optional low-level driver, checksum, and optional icon format are specified in [Expansion Card EEPROM.md](Expansion%20Card%20EEPROM.md).

