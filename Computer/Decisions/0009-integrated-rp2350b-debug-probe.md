# 0009 — Integrated RP2350B Debug Probe

**Status:** Accepted

## Decision

The Default Mainboard includes a second **RP2350B** dedicated to in-system debugging and flashing.

This controller runs modified Picoprobe firmware.

## Rationale

An RP2040 would have a lower component cost for the debug-probe role.

However, using a different MCU would increase pick-and-place machine setup/changeover cost. Using the same RP2350B package for both the system CPU and debug probe is expected to cost less overall than introducing a separate RP2040 placement setup.

## Consequences

- The Default Mainboard contains two RP2350B devices.
- The second RP2350B is not part of the system CPU resources.
- Debugging and flashing are available without an external probe.
- The debug-probe firmware is a modified Picoprobe implementation.
