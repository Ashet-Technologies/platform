# 0007 — ENC624J600 Ethernet Controller

**Status:** Accepted

## Decision

Use the **Microchip ENC624J600** as the Ethernet controller of the Default Mainboard.

## Rationale

The ENC624J600 is a capable 10/100 Ethernet controller that can be controlled through either SPI or a parallel host interface.

The parallel interface provides a path toward theoretical 100 Mbit/s operation, while SPI permits a simpler implementation when maximum throughput is not required.

## Consequences

- Ethernet does not consume the RP2350 USB interface.
- The Default Mainboard may choose between SPI and the parallel host interface according to implementation needs.
- Ethernet performance depends on the selected host interface and software implementation.
