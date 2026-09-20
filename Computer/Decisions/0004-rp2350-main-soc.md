# 0004 — RP2350 as the Main SoC

**Status:** Accepted

## Decision

Use the Raspberry Pi RP2350 as the main SoC of the Ashet Home Computer Mainboard.

## Rationale

The RP2350 was selected because it is:

- inexpensive
- supported by a broad software ecosystem, including Pico SDK, MicroZig, MicroPython, TinyGo, and similar tooling
- well documented
- capable of running Arm Cortex-M33 or RISC-V cores
- able to support up to 16 MiB of RAM in the intended system architecture

These properties make it suitable both for the finished computer and for development, experimentation, and alternative software stacks.

## Consequences

- The concrete Mainboard architecture follows RP2350 capabilities and constraints.
- Both Arm and RISC-V execution are available to Mainboard software.
- The platform can benefit from the existing RP2350 software and tooling ecosystem.
