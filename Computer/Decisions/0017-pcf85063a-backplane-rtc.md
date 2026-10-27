# 0017 — PCF85063A Backplane RTC

**Status:** Accepted

## Decision

Use the **PCF85063A** as the Backplane-provided real-time clock for the Ashet Home Computer.

The PCF85063A has the fixed 7-bit I²C address `0x51`. The RTC is connected to the system-management I²C subnet and occupies the platform RTC address `0x51`.

This decision supersedes [0014 — PCF8523 Backplane RTC](0014-pcf8523-backplane-rtc.md).

## Rationale

The previously selected PCF8523 is obsolete and will no longer be available for the system. The PCF85063A replaces it as the Backplane RTC.

## Consequences

- The concrete Ashet Home Computer Backplane uses a PCF85063A instead of a PCF8523.
- The platform RTC allocation on the system-management I²C subnet changes from `0x68` to `0x51`.
- Software addressing the Backplane RTC uses the fixed 7-bit I²C address `0x51`.
