# 0002 — Per-Slot I²C Bus via PCA9547

**Status:** Accepted

## Decision

Each Expansion Card slot receives its own I²C bus segment.

The Backplane uses a PCA9547 I²C multiplexer to select which Expansion Card bus segment is connected to the host-side I²C controller.

## Rationale

Earlier revisions used three address-selection lanes per Expansion Card. Those lanes had to be routed to the card metadata EEPROM so that each card could appear at a distinct address on a shared I²C bus.

That approach made both the Backplane and Expansion Card designs architecturally more complex:

- each slot required dedicated address-selection wiring
- every metadata EEPROM had to incorporate those address signals
- Expansion Card designs were not electrically identical with respect to I²C addressing

Using the PCA9547 removes the need for per-slot address-selection lanes.

Each Expansion Card now sees the same I²C interface and can use the same fixed metadata EEPROM address.

## Consequences

- All Expansion Card slots are equivalent from the card's I²C point of view.
- Expansion Cards do not need slot-dependent EEPROM address wiring.
- The Backplane is responsible for selecting the active Expansion Card I²C segment.
- Each Expansion Card effectively receives a full I²C address space except for addresses reserved by the platform.
