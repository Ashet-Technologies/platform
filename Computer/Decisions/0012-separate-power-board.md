# 0012 — Separate Power Board

**Status:** Accepted

## Decision

The Backplane does not contain the system power regulators.

Instead, the Backplane provides a connector for a separate internal **Power Board**. The Power Board generates the required system power rails.

## Rationale

Separating power generation from the Backplane's functional logic allows the power system and Backplane to be developed independently.

It also simplifies laboratory and measurement work because the Backplane can be powered from development supplies without depending on the final PSU design.

The separate Power Board also allows user-replaceable power implementations, including:

- an integrated mains-powered supply
- a battery-powered supply
- a USB-C-powered supply

## Consequences

- Power-regulator design is isolated from the Backplane design.
- The Backplane requires a defined internal Power Board connector.
- Alternative Power Boards can be developed without redesigning the Backplane logic.
- The computer can use different power-source strategies while retaining the same Backplane.
