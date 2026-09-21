# Mainboard Interface

This document defines the semantics of the Mainboard-facing connector signals.

Connector pin locations are defined in [Pinout.md](Pinout.md).

## Signals

| Signal | Semantics |
| --- | --- |
| `GND` | Signal and power ground. |
| `+12V` | 12 V power rail. |
| `+5V` | 5 V power rail. |
| `+3V3` | 3.3 V power rail. |
| `I2C_SCL` | System I²C bus clock lane. 3.3 V, open collector. Connected only to the PCA9547 on the Backplane. |
| `/SLOT_AUDIO` | Connected to GND on the Backplane when the Backplane provides the Audio bus. |
| `I2C_SDA` | System I²C bus data lane. 3.3 V, open collector. Connected only to the PCA9547 on the Backplane. |
| `/SLOT_VIDEO` | Connected to GND on the Backplane when the Backplane provides the high-speed bus. |
| `/FAB_RESET` | Fabric reset. Resets the Southbridge. Pulled down to GND through 10 kΩ by default. |
| `/SLOT_USB0` | Connected to GND on the Backplane when the Backplane uses the USB0 bus. |
| `/RESET` | System reset. Open collector. Defaults to 3.3 V. A power-on reset is generated on system power-up. |
| `/SLOT_USB1` | Connected to GND on the Backplane when the Backplane uses the USB1 bus. |
| `/PRESENT` | Presence indication. The Mainboard must connect this signal to GND through 0 Ω, identical to an Expansion Card. |
| `CLK` | Global 48 MHz clock. |
| `HSTX0..HSTX7` | Eight high-speed lanes connected directly to an Expansion Card slot. |
| `GP0..GP7` | Eight signals connected to Propeller 2 pins P56..P63. |
| `I2S_SDIN` | Default input data lane of the Audio bus. |
| `USB0_D+` | D+ lane of USB0, driven by the Mainboard. |
| `I2S_SDOUT` | Default output data lane of the Audio bus. |
| `USB0_D-` | D− lane of USB0, driven by the Mainboard. |
| `I2S_BCLK` | Bit clock of the Audio bus, driven by the Mainboard. |
| `USB1_D+` | D+ lane of USB1, driven by the Mainboard. |
| `I2S_WCLK` | Word clock of the Audio bus, driven by the Mainboard. |
| `USB1_D-` | D− lane of USB1, driven by the Mainboard. |
| `I2S_MCLK` | Master clock of the Audio bus, driven by the Mainboard. Its frequency must be 256..512 times the frequency of `I2S_WCLK`, up to 50 MHz. |

Signal names prefixed with `/` are active-low.
