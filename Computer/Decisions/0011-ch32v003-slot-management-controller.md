# 0011 — CH32V003 Slot Management Controllers

**Status:** Accepted

## Decision

Use the **CH32V003** as the internal board-management controller for Expansion Slots.

The Backplane may use one CH32V003 per Expansion Slot. Each controller provides the same management features and firmware interface, using a distinct I²C address.

## Rationale

The CH32V003 is substantially cheaper than the I²C or SPI port expanders considered for this design while also providing ADC capability and general-purpose firmware-controlled behavior.

Using one controller per Expansion Slot gives every slot the same local management capabilities.

## Consequences

- The seven Expansion Slots can use seven identical CH32V003 management controllers.
- Slot-management behavior can be implemented once and reused for every slot.
- Each controller must have a unique I²C address on the relevant management bus.
- ADC-based monitoring functions are available without a separate ADC device.
