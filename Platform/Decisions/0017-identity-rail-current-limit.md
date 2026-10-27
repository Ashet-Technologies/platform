# 0017 — Identity Rail 20 mA Current Limit

**Status:** Accepted

## Decision

Each Expansion Slot provides `+3V3_ID` at 3.3 V, independently of the three main slot power rails. An Expansion Card must not draw more than 20 mA from this supply. This is a card-side consumption limit, not a requirement for hardware-enforced current limiting.

## Rationale

A 20 mA budget permits an inexpensive Backplane implementation in which a CH32V003 GPIO directly drives the identity supply, without requiring a dedicated power switch. The supply is intended only for the card identity provider and its I²C pull-ups.

## Consequences

- An Expansion Card's identity circuit and I²C pull-ups must operate within the shared 20 mA limit.
- The Backplane may use a suitable GPIO-driven implementation or a separate power switch, provided the required 3.3 V supply and independent switching are met. No current-limiting or protective device is required; protection against faulty cards is recommended. See [0019 — Expansion Rail Current Budgets and Optional Protection](0019-power-rail-current-budgets.md).
