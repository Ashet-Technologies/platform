# 0003 — Metadata EEPROM Address 0x57

**Status:** Accepted

## Decision

The Expansion Card metadata EEPROM uses I²C address **0x57** instead of the common default address **0x50**.

## Rationale

Expansion Cards may use their I²C bus for additional devices and protocols.

Using `0x50` for the mandatory platform metadata EEPROM would create unnecessary conflicts with other EEPROM-like devices that conventionally use that address.

A concrete example is DDC/EDID, which uses address `0x50`.

Placing the platform metadata EEPROM at `0x57` avoids this common conflict while still keeping the EEPROM in the conventional EEPROM address range.

## Consequences

- Expansion Card metadata EEPROMs must respond at `0x57`.
- `0x50` remains available to Expansion Card functionality such as DDC/EDID.
- Expansion Card designs are less likely to require address-remapping circuitry for common peripherals.
