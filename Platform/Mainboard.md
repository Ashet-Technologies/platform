# Mainboard Interface

This document defines the semantics of the Mainboard-facing connector signals.

Connector pin locations are defined in [Pinout.md](Pinout.md).

Signal names prefixed with `/` are active-low.

## Power

| Signal | Semantics |
| --- | --- |
| `GND` | Signal and power ground. |
| `+12V` | 12 V power rail, up to 500 mA. |
| `+5V` | 5 V power rail, up to 500 mA. |
| `+3V3` | 3.3 V power rail, up to 500 mA. |

## Management and Control

| Signal | Semantics |
| --- | --- |
| `I2C_SCL` | System I²C bus clock lane. 3.3 V, open collector. Connected only to the PCA9547 on the Backplane. |
| `I2C_SDA` | System I²C bus data lane. 3.3 V, open collector. Connected only to the PCA9547 on the Backplane. |
| `/FAB_RESET` | Fabric reset. Resets the Southbridge. The Mainboard does not provide a bias resistor; the Backplane biases A8 according to the Slot role. |
| `/RESET` | System reset. Open collector. Defaults to 3.3 V. A power-on reset is generated on system power-up. |
| `/PRESENT` | Presence indication. The Mainboard must connect this signal to GND through 0 Ω, identical to an Expansion Card. |
| `CLK` | Global 48 MHz clock. |
| `/SLOT_AUDIO` | Connected to GND on the Backplane when the Backplane provides the Audio bus. |
| `/SLOT_HS` | Connected to GND on the Backplane when the Backplane provides the High-Speed bus. |
| `/SLOT_USB0` | Connected to GND on the Backplane when the Backplane uses the USB0 bus. |
| `/SLOT_USB1` | Connected to GND on the Backplane when the Backplane uses the USB1 bus. |

## High-Speed Interface

| Signal | Semantics |
| --- | --- |
| `HSTX0..HSTX7` | Eight high-speed lanes connected directly to an Expansion Slot. |

## Southbridge Interface

| Signal | Semantics |
| --- | --- |
| `GP0..GP7` | Eight signals connected to Propeller 2 pins P56..P63. |

## Audio Bus

| Signal | Semantics |
| --- | --- |
| `I2S_SDIN` | Default input data lane of the Audio bus. |
| `I2S_SDOUT` | Default output data lane of the Audio bus. |
| `I2S_BCLK` | Bit clock of the Audio bus, driven by the Mainboard. |
| `I2S_WCLK` | Word clock of the Audio bus, driven by the Mainboard. |
| `I2S_MCLK` | Master clock of the Audio bus, driven by the Mainboard. Its frequency must be 256..512 times the frequency of `I2S_WCLK`, up to 50 MHz. |

## USB

USB0 and USB1 are **USB host ports** of the Mainboard Slot.

The Backplane expects either a USB host or no connection on these pin pairs. A Mainboard must not expose a USB device on either pair.

| Signal | Semantics |
| --- | --- |
| `USB0_D+` | D+ lane of USB0 host port. Driven by the Mainboard when USB0 is implemented; otherwise N.C. |
| `USB0_D-` | D− lane of USB0 host port. Driven by the Mainboard when USB0 is implemented; otherwise N.C. |
| `USB1_D+` | D+ lane of USB1 host port. Driven by the Mainboard when USB1 is implemented; otherwise N.C. |
| `USB1_D-` | D− lane of USB1 host port. Driven by the Mainboard when USB1 is implemented; otherwise N.C. |

## A8 Dual-Function Pin

A8 is a dual-function pin whose bias is provided by the Backplane according to the Slot role. The Mainboard itself does not provide a pull-up or pull-down on A8:

- In the **Mainboard Slot**, the Backplane pulls `/FAB_RESET` down to GND through 10 kΩ.
- In an **Expansion Slot**, the Backplane pulls A8 up to `+3V3` through 10 kΩ.

This allows a Mainboard to detect whether it is installed in the Mainboard Slot or in an Expansion Slot. A Mainboard that supports this capability may switch into **Not-so-mainboard** mode when installed in an Expansion Slot, allowing it to operate as a coprocessor and accept commands or tasks from the host system.

The exact Not-so-mainboard operating mode and protocol are not yet specified.
