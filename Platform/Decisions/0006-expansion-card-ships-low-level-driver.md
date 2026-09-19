# 0006 — Expansion Card Low-Level Driver Selection

**Status:** Accepted

## Decision

An Expansion Card may embed a card-specific Propeller 2 low-level driver in its EEPROM.

Cards that can use a platform-standard low-level driver interface may omit card-specific firmware and identify the standard interface through the EEPROM `Driver Interface` field.

During card initialization, the operating system loads the embedded low-level driver when `Has Firmware` is set. Otherwise, it uses the selected platform-standard driver interface.

The standard driver interfaces and their identifiers are not yet specified.

## Rationale

A card-specific low-level driver defines how the Propeller 2 drives and samples that Expansion Card's electrical interface.

Shipping such a driver together with the card prevents mismatches between the driver and the physical hardware revision: the hardware and the code that operates its electrical interface are distributed together.

Cards using a common, standardized electrical interface do not need to duplicate identical firmware in every EEPROM. They can instead select a platform-standard driver.

## Consequences

- Card-specific low-level firmware is optional.
- Cards with custom electrical or protocol behavior can ship the matching Propeller 2 driver in their EEPROM.
- Cards using a platform-standard interface can omit embedded firmware.
- Hardware revisions with custom firmware can carry a correspondingly updated low-level driver without requiring an operating-system update.
- The EEPROM format and driver-selection mechanism are part of the platform compatibility contract.
- Embedded drivers must obey the electrical and timing constraints defined by the Platform specification.
