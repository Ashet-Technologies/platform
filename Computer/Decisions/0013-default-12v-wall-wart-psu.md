# 0013 — Default 12 V Wall-Wart Power Supply

**Status:** Accepted

## Decision

Use a ready-made external **12 V wall-wart power adapter** connected through a barrel jack as the default power source for the computer.

## Rationale

Ready-made 12 V barrel-jack adapters are inexpensive and widely available.

Using an external prebuilt adapter avoids designing and validating a mains-powered PSU for the first computer implementation.

## Consequences

- The default computer accepts 12 V through a barrel jack.
- The internal Power Board derives the required system rails from the supplied 12 V input.
- Alternative Power Boards and power sources remain possible.
- The project does not need to develop an integrated mains PSU for the default configuration.
