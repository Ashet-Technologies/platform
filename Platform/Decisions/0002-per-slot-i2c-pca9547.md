# 0002 — Per-Slot I²C Bus via PCA9547

**Status:** Accepted

## Decision

Each Expansion Slot receives its own I²C bus segment.

The PCA9547 I²C multiplexer is physically located on the Backplane. The Mainboard controls it to select which downstream I²C segment is connected to the host-side I²C controller.

Seven downstream channels are assigned one-to-one to Expansion Slots 0 through 6. The eighth downstream channel is the Backplane-local system-management I²C subnet.

## Rationale

Earlier revisions used three address-selection lanes per Expansion Card. Those lanes had to be routed to the card metadata EEPROM so that each card could appear at a distinct address on a shared I²C bus.

That approach made both the Backplane and Expansion Card designs architecturally more complex:

- each slot required dedicated address-selection wiring
- every metadata EEPROM had to incorporate those address signals
- Expansion Card designs were not electrically identical with respect to I²C addressing

Using the PCA9547 removes the need for per-slot address-selection lanes.

Each Expansion Card now sees the same I²C interface and can use the same fixed metadata EEPROM address.

## Consequences

- All Expansion Slots are equivalent from the card's I²C point of view.
- Expansion Cards do not need slot-dependent EEPROM address wiring.
- The PCA9547 is physically on the Backplane; the Mainboard controls it to select the active downstream I²C segment.
- The eighth downstream channel is reserved for the Backplane-local system-management I²C subnet.
- Each Expansion Card effectively receives a full I²C address space except for addresses reserved by the platform.
