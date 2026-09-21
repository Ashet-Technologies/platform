# 0012 — Flexible HSTX Interface

**Status:** Accepted

## Decision

Keep the eight HSTX lanes protocol-agnostic at the Platform level.

The Expansion Card driver selects an HSTX interface definition. That interface definition specifies the protocol and direction of each HSTX pin.

The Platform recognizes standard interface definitions so software can determine compatibility without understanding every card-specific driver.

Currently recognized interface classes are:

- Unused
- Custom
- DVI
- QSPI
- QPI
- MIPI-DSI, 1 lane
- MIPI-DSI, 2 lanes
- MIPI-CSI

The exact pin mappings for the standard interfaces are to be specified separately.

## Rationale

Hard-wiring the high-speed lanes to one protocol would unnecessarily constrain future Mainboards and Expansion Cards.

Keeping the pins flexible allows future electrical/protocol uses while standard interface definitions preserve interoperability for common cases.

A host can quickly determine whether it supports a standard HSTX interface, while custom hardware remains possible through the Custom interface.

## Consequences

- HSTX direction is defined by the selected interface, not globally by the connector.
- Unused requires both sides to leave the HSTX pins high-impedance.
- Custom delegates protocol handling to the Expansion Card driver.
- Standard interface identifiers provide a compatibility check between Mainboard and Expansion Card.
- Standard HSTX pinouts remain an open specification topic.
