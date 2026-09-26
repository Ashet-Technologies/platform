# System Management I²C Subnet

The Backplane-local system-management I²C subnet contains the Backplane and Expansion Slot management devices used by the Mainboard.

The subnet reserves the following 7-bit I²C addresses:

| Address | Device |
| ---: | --- |
| `0x20` | Expansion Slot 0 management controller |
| `0x21` | Expansion Slot 1 management controller |
| `0x22` | Expansion Slot 2 management controller |
| `0x23` | Expansion Slot 3 management controller |
| `0x24` | Expansion Slot 4 management controller |
| `0x25` | Expansion Slot 5 management controller |
| `0x26` | Expansion Slot 6 management controller |
| `0x27` | Backplane board-management controller |
| `0x57` | Backplane configuration EEPROM |
| `0x68` | Real-time clock |
| `0x77` | Reserved by the Platform |

The RTC allocation at `0x68` is compatible with both the PCF8523 and DS1307. These devices use the same address and are therefore alternative RTC implementations, not simultaneously addressable devices on this subnet.

The rationale for the board-management-controller address range is documented in [Decisions/0016-board-management-i2c-address-range.md](Decisions/0016-board-management-i2c-address-range.md).

