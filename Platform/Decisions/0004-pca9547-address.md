# 0004 — PCA9547 Address 0x77

**Status:** Accepted

## Decision

The PCA9547 I²C multiplexer uses address **0x77**.

## Rationale

The multiplexer is placed at the high end of its configurable I²C address range.

This follows the same design principle as placing the metadata EEPROM at `0x57`: reserve platform infrastructure at high addresses and leave the more commonly used lower/default addresses available to Expansion Card designs.

Using `0x77` therefore reduces the likelihood of conflicts and simplifies Expansion Card design.

## Consequences

- `0x77` is reserved by the platform.
- Expansion Cards must not use `0x77`.
- The host uses `0x77` to control the PCA9547 and select an Expansion Card I²C segment.
