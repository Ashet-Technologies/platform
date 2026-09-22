# 0014 — PCF8523 Backplane RTC

**Status:** Accepted

## Decision

Use the **PCF8523** as the Backplane-provided real-time clock for the Ashet Home Computer.

The RTC is connected to the system-management I²C subnet and occupies the platform RTC address `0x68`.

## Rationale

The PCF8523 is less expensive than the DS1307 while providing the required real-time-clock functionality.

## Consequences

- The concrete Ashet Home Computer Backplane uses a PCF8523 rather than a DS1307.
- The RTC uses the platform-defined `0x68` RTC allocation on the system-management I²C subnet.
