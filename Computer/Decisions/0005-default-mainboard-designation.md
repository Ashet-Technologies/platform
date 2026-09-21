# 0005 — Default Mainboard Designation

**Status:** Accepted

## Decision

Call the first fully realized Ashet Home Computer Mainboard design the **Default Mainboard**.

The Default Mainboard is the reference Mainboard intended to be built first. It is not the only Mainboard concept planned for the computer.

## Rationale

The computer is designed around a replaceable Mainboard and the Platform intentionally permits alternative Mainboard implementations.

Calling the first implementation simply "the Mainboard" would blur the distinction between the Platform concept and this concrete hardware design.

## Consequences

- Documentation for this concrete hardware uses the name **Default Mainboard**.
- Other Mainboard designs may coexist with the Default Mainboard.
- Platform requirements must not depend on implementation details unique to the Default Mainboard.
