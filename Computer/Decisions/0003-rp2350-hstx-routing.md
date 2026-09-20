# 0003 — RP2350 HSTX Routing

**Status:** Accepted

## Decision

Route the eight Platform HSTX lanes to HSTX-capable pins of the RP2350.

The physical routing does not require use of the RP2350 HSTX peripheral. The same pins may be driven or sampled through PIO or other applicable RP2350 peripherals when the selected Platform interface requires it.

## Rationale

Routing the lanes to HSTX-capable pins provides an efficient path for high-speed video output while retaining the RP2350's programmable peripheral flexibility.

This supports both standard HSTX interfaces and future/custom interfaces without changing the Mainboard routing.

## Consequences

- The current Mainboard can use the RP2350 HSTX peripheral for interfaces that fit it.
- PIO or other peripherals may implement alternative HSTX protocols.
- Platform HSTX flexibility does not require a Mainboard PCB redesign for every supported protocol.
