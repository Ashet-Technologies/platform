# 0015 — Expansion Card Slot Guide Rails

**Status:** Accepted

## Decision

Each Expansion Card Slot must include **guard rails** that guide an Expansion Card into its PCI Express x4 connector during insertion.

## Rationale

Without guard rails, installing an Expansion Card requires locating the approximately 2 mm connector slot while the connector is effectively out of sight.

This makes card insertion unnecessarily difficult and has a significant negative impact on usability and haptics.

The guard rails constrain and align the card before its edge reaches the connector, making insertion straightforward and repeatable.

## Consequences

- Expansion Card Slots include mechanical guide rails for Expansion Cards.
- Expansion Cards are aligned with the PCI Express x4 connector before insertion into the connector.
- Blind card insertion is substantially easier.
- The guide rails contribute to the snug mechanical fit of an installed Expansion Card.
