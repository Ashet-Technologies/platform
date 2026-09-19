# 0001 — Propeller 2 Bootstrap and Mainboard Interface

**Status:** Accepted

## Decision

The **Propeller 2 is part of the Ashet platform interface** and is the Southbridge implementation defined by the platform.

The Propeller 2 does not contain persistent platform firmware. Instead, the Mainboard bootstraps the Propeller 2 during system startup by loading the Southbridge firmware into it.

Because the Mainboard supplies the Southbridge firmware, that firmware defines the **logical and electrical interface between the Mainboard and the Southbridge**, as long as the resulting interface remains within the electrical, timing, pinout, and other constraints defined by the platform specification.

## Rationale

This keeps the Mainboard-to-Southbridge protocol evolvable without requiring persistent firmware storage on the Backplane.

The physical platform contract remains stable, while the firmware loaded by the Mainboard can define how the available signals are used and which protocol runs across them.

## Consequences

- A platform-compatible Backplane uses a Propeller 2 as its Southbridge.
- The Backplane does not need persistent storage for Southbridge firmware.
- The Mainboard is responsible for bootstrapping the Propeller 2 before using Southbridge services.
- Mainboard firmware and Southbridge firmware are coupled and may evolve together.
- The firmware may change the logical protocol and electrical use of configurable signals, but must remain within the constraints defined by the platform interface.
- Expansion Card compatibility must continue to follow the separately defined Expansion Bus contract.
