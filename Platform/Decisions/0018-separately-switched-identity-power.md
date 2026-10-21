# 0018 — Separately Switched Identity Power

**Status:** Accepted

## Decision

The `+3V3_ID` supply is switched separately from the three main Expansion Slot power rails. The Backplane can power and communicate with the identity provider while the main slot power is disabled, and can turn the identity supply off independently.

## Rationale

Independent identity power allows the platform to inspect card metadata before enabling its main power rails. This provides a path to evaluate card power requirements before power-on; the metadata fields defining those requirements are not yet specified.

The platform can also identify installed cards whose main slot power has not been enabled or whose activation has failed, and present that information to the user.

## Consequences

- Identity discovery does not require enabling the main slot power rails.
- The identity provider and its I²C pull-ups are powered only when `+3V3_ID` is enabled.
- The identity power domain must not back-power the main switched supply domains.
- The Expansion Slot activation sequence enables `+3V3_ID` before EEPROM access.
