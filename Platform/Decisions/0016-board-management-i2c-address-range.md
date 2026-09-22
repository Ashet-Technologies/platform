# 0016 — Board-Management I²C Address Range

**Status:** Accepted

## Decision

The system-management I²C subnet reserves addresses **0x20 through 0x27** for board-management controllers.

The address assignment is:

- Expansion Slot 0 management controller: `0x20`
- Expansion Slot 1 management controller: `0x21`
- Expansion Slot 2 management controller: `0x22`
- Expansion Slot 3 management controller: `0x23`
- Expansion Slot 4 management controller: `0x24`
- Expansion Slot 5 management controller: `0x25`
- Expansion Slot 6 management controller: `0x26`
- Backplane board-management controller: `0x27`

## Rationale

The I²C address range `0x20..0x27` is commonly used by GPIO/port expanders.

The Ashet platform does not use regular port expanders on the system-management subnet, so this contiguous eight-address range is used for the board-management system instead.

The range also maps naturally to the seven Expansion Slot controllers plus the Backplane board-management controller.

## Consequences

- Expansion Slot management-controller addresses are derived directly from the zero-based slot number: `0x20 + slot`.
- The Backplane board-management controller has the fixed address `0x27`.
- Devices added to the system-management subnet must not use `0x20..0x27` for other purposes.
