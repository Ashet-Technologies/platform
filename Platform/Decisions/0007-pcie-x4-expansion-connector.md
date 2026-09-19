# 0007 — PCI Express x4 Expansion Connector

**Status:** Accepted

## Decision

Use a **PCI Express x4 card-edge connector** as the physical connector for the Expansion Card interface.

The connector was selected because it provides:

- 64 contacts
- electrical suitability for high-speed signals
- low cost and broad availability

## Alternatives Considered

A conventional 0.1-inch-pitch connector would have been preferable from a prototyping and hand-assembly perspective.

However, suitable high-pin-count 0.1-inch-pitch connector systems are substantially more expensive than PCI Express x4 connectors.

## Mitigation

To preserve easy prototyping despite the finer-pitch PCI Express connector, the platform provides an official, blessed **perfboard Expansion Card PCB design**.

This board adapts the standard Expansion Card connector to a layout intended for hand-built circuitry and experimentation.

## Consequences

- Production Expansion Cards use a cheap, high-density, high-speed-capable connector.
- The physical connector remains readily obtainable.
- Direct hand-wiring to the connector is less convenient than with 0.1-inch-pitch headers.
- The official perfboard Expansion Card provides the supported path for low-volume prototypes and hobbyist designs.
