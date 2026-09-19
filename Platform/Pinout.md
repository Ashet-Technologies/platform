# Ashet Platform Pinout

This document mirrors the current `Pin Out` sheet from the **Ashet Home Computer Design** spreadsheet.

The connector uses two 32-position sides, A and B.

## Mainboard- and Expansion-Facing Mapping

| Pin | A Side, Mainboard | A Side, Expansion | B Side, Expansion | B Side, Mainboard |
| --: | ----------------- | ----------------- | ----------------- | ----------------- |
| 1  | GND         | GND         | GND          | GND          |
| 2  | +12V        | +12V        | +12V         | +12V         |
| 3  | +5V         | +5V         | +5V          | +5V          |
| 4  | +3V3        | +3V3        | +3V3         | +3V3         |
| 5  | GND         | GND         | GND          | GND          |
| 6  | I2C_SCL     | I2C_SCL     | /SLOT_AUDIO  | /SLOT_AUDIO  |
| 7  | I2C_SDA     | I2C_SDA     | /SLOT_VIDEO  | /SLOT_VIDEO  |
| 8  | /FAB_RESET  | +3V3 10K    | /SLOT_FUNC0  | /SLOT_USB0   |
| 9  | /RESET      | /RESET      | /SLOT_FUNC1  | /SLOT_USB1   |
| 10 | GND         | GND         | /PRESENT     | /PRESENT     |
| 11 | CLK         | CLK         | GND          | GND          |
| 12 | GND         | GND         | GND          | GND          |
| 13 | HSTX0       | HSTX0       | GP0          | GP0          |
| 14 | HSTX1       | HSTX1       | GP1          | GP1          |
| 15 | GND         | GND         | GND          | GND          |
| 16 | HSTX2       | HSTX2       | GP2          | GP2          |
| 17 | HSTX3       | HSTX3       | GP3          | GP3          |
| 18 | GND         | GND         | GND          | GND          |
| 19 | HSTX4       | HSTX4       | GP4          | GP4          |
| 20 | HSTX5       | HSTX5       | GP5          | GP5          |
| 21 | GND         | GND         | GND          | GND          |
| 22 | HSTX6       | HSTX6       | GP6          | GP6          |
| 23 | HSTX7       | HSTX7       | GP7          | GP7          |
| 24 | GND         | GND         | GND          | GND          |
| 25 | I2S_SDIN    | I2S_SDIN    | RESERVED0    | USB0_D+      |
| 26 | I2S_SDOUT   | I2S_SDOUT   | RESERVED1    | USB0_D-      |
| 27 | GND         | GND         | RESERVED2    | GND          |
| 28 | I2S_BCLK    | I2S_BCLK    | RESERVED3    | USB1_D+      |
| 29 | I2S_WCLK    | I2S_WCLK    | RESERVED4    | USB1_D-      |
| 30 | GND         | GND         | RESERVED5    | GND          |
| 31 | I2S_MCLK    | I2S_MCLK    | RESERVED6    | RESERVED0    |
| 32 | GND         | GND         | GND          | GND          |

## Notes

- `/FAB_RESET`, `/SLOT_USB0`, `/SLOT_USB1`, `USB0_D+`, `USB0_D-`, `USB1_D+`, and `USB1_D-` are present only in the mainboard-side mapping from the sheet and are not currently part of the generic Expansion Bus signal specification.
- Expansion pin A8 is labeled `+3V3 10K` in the sheet.
- The expansion-facing mapping exposes `RESERVED0` through `RESERVED6`. The signal specification lists eight reserved signals in total, but the sheet does not identify an expansion-facing `RESERVED7`.
- No interpretation beyond the sheet values is applied here.
