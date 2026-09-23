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
| `/FAB_RESET` | Fabric reset and Mainboard mode-detection lane. Resets the Southbridge. The Mainboard does not provide a bias resistor; the Backplane biases A8 according to the Slot role. |
| `/RESET` | System reset signal. Open collector. Defaults to 3.3 V. A power-on reset is generated on system power-up. Mode-specific behavior is defined below. |
| `/PRESENT` | Presence indication. The Mainboard must connect this signal to GND through 0 Ω, identical to an Expansion Card. |
| `CLK` | Global 48 MHz clock. |
| `/SLOT_AUDIO` | Connected to GND on the Backplane when the Backplane provides the Audio bus. |
| `/SLOT_HS` | Connected to GND on the Backplane when the Backplane provides the High-Speed bus. |
| `/SLOT_USB0` | Connected to GND on the Backplane when the Backplane uses the USB0 bus. |
| `/SLOT_USB1` | Connected to GND on the Backplane when the Backplane uses the USB1 bus. |

## Startup Mode Selection

On every boot, a Mainboard must sample `/FAB_RESET`.

- If `/FAB_RESET` is **LOW**, the Mainboard boots into regular Mainboard mode.
- If `/FAB_RESET` is **HIGH**:
  - a Mainboard that supports **Not-so-mainboard mode** must boot into that mode
  - a Mainboard that does not support Not-so-mainboard mode must enter **inert mode**

A Mainboard may react to `/RESET` going low. If that reaction causes the Mainboard to boot again, it must resample `/FAB_RESET`.

### Inert Mode

An inert Mainboard must not drive the following signals and must configure them as high-impedance:

- `I2C_*`
- `HSTX0..HSTX7`
- `GP0..GP7`
- `I2S_*`
- `USB0_*` and `USB1_*`

### Not-so-mainboard Mode

The name **Not-so-mainboard** is read as **"(not-so-main) board"**, not as **"not-so (mainboard)"**.

After detecting `/FAB_RESET` HIGH, a Mainboard entering Not-so-mainboard mode must immediately keep these interfaces high-impedance:

- `USB0_*` and `USB1_*`
- `HSTX0..HSTX7`

The Mainboard must switch its I²C interface into device mode and emulate the Expansion Card metadata EEPROM at address `0x57`, using the format defined in [Expansion Card EEPROM.md](Expansion%20Card%20EEPROM.md).

The emulated EEPROM must contain a card-specific Low-Level-Driver. Consequently, its metadata must set `Has Firmware`, and the firmware block must contain the Low-Level-Driver image.

The standard EEPROM image occupies the platform-defined address space below the 8 KiB boundary. A Not-so-mainboard may expose additional implementation-specific features at addresses starting at `0x2000`. Such extensions are outside the standard Expansion Card EEPROM format.

While `/RESET` is LOW, the Mainboard must keep `GP0..GP7` high-impedance. After `/RESET` is HIGH, the Mainboard may establish the Low-Level-Driver-defined interface on those lanes.

The Mainboard may enable I²S later only if the Expansion Slot provides the Audio feature and driver negotiation has enabled its use. Until then, the I²S interface remains disabled.

The Mainboard may enable HSTX later only if the Expansion Slot provides the High-Speed feature and driver negotiation has enabled its use. Until then, `HSTX0..HSTX7` remain high-impedance.

The current Expansion Slot initialization sequence reads the metadata EEPROM while `/RESET` is asserted. A Not-so-mainboard therefore needs to remain capable of servicing I²C while reset is asserted. Whether `/RESET` must be treated as a software-observed reset request in this mode, or whether the slot initialization sequence should change, remains an open topic.

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
| `I2S_BCLK` | Bit clock of the Audio bus, driven by the Mainboard when the Audio interface is enabled. |
| `I2S_WCLK` | Word clock of the Audio bus, driven by the Mainboard when the Audio interface is enabled. |
| `I2S_MCLK` | Master clock of the Audio bus, driven by the Mainboard when the Audio interface is enabled. Its frequency must be 256..512 times the frequency of `I2S_WCLK`, up to 50 MHz. |

## USB

In regular Mainboard mode, USB0 and USB1 are **USB host ports** of the Mainboard Slot.

The Backplane expects either a USB host or no connection on these pin pairs. A Mainboard must not expose a USB device on either pair.

In inert mode and Not-so-mainboard mode, these pins remain high-impedance as specified above.

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

This lets a Mainboard detect whether it is installed in the Mainboard Slot or in an Expansion Slot. The sampled state selects regular Mainboard, Not-so-mainboard, or inert operation according to the startup rules above.
