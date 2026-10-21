# 0017 — Identity Rail 20 mA Current Limit

**Status:** Accepted

## Decision

Each Expansion Slot provides `+3V3_ID` at 3.3 V with a current limit of 20 mA, independently of the three main slot power rails.

## Rationale

A 20 mA budget permits an inexpensive Backplane implementation in which a CH32V003 GPIO directly drives the identity supply, without requiring a dedicated power switch. The supply is intended only for the card identity provider and its I²C pull-ups.

## Consequences

- An Expansion Card's identity circuit and I²C pull-ups must operate within the shared 20 mA limit.
- The Backplane may use a suitable GPIO-driven implementation or a separate power switch, provided the required 3.3 V supply, switching, and current limit are met.
