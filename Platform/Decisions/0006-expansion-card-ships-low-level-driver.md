# 0006 — Expansion Card Ships Its Low-Level Driver

**Status:** Accepted

## Decision

Each Expansion Card stores the Propeller 2 low-level expansion driver required to operate that card in its metadata EEPROM.

During card initialization, the operating system reads the low-level driver from the card EEPROM and loads it into the Propeller 2 Southbridge.

The operating system therefore does not need to carry a matching built-in low-level driver for every Expansion Card.

## Rationale

The low-level driver defines how the Propeller 2 drives and samples the Expansion Card's electrical interface.

Keeping that driver separately in the operating system creates a version-matching problem: the installed driver could describe a different electrical interface or hardware revision than the physically installed card.

Shipping the low-level driver together with the Expansion Card solves this problem.

The card and its driver are distributed as one hardware/software unit, so the driver stored in the EEPROM can always be made to match the actual electrical interface of that card revision.

## Consequences

- The low-level driver and the Expansion Card hardware are versioned and shipped together.
- The operating system does not need a database of card-specific Propeller 2 low-level drivers.
- Card initialization can be self-contained: identify the card, read its low-level driver, and load that driver into the Southbridge.
- Hardware revisions can carry correspondingly updated low-level drivers without requiring an operating-system update.
- The EEPROM format and driver-loading mechanism are part of the platform compatibility contract.
- The loaded driver must still obey the electrical and timing constraints defined by the Platform specification.
