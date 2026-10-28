# 0019 — Expansion Rail Current Budgets and Optional Protection

**Status:** Accepted

## Decision

The Expansion Bus specifies the maximum current an Expansion Card is permitted to draw from each power rail, per Expansion Slot:

| Rail | Maximum permitted current draw |
| --- | ---: |
| `+3V3` | 500 mA |
| `+5V` | 500 mA |
| `+12V` | 500 mA |
| `+3V3_ID` | 20 mA |

These are **requirements on the Expansion Card's current consumption**, not guarantees that the Backplane will detect, limit, or interrupt overcurrent. The current budget for each rail applies across all connector contacts for that rail.

The Platform does **not** require an eFuse, PTC, dedicated current limiter, or other hardware protection on any of these rails. Implementations are recommended to include appropriate protection against faulty Expansion Cards, but may omit it.

## Rationale

The current budgets define compliant Expansion Card behavior without mandating a particular protection circuit or hardware cost for every Backplane implementation.

## Consequences

- An Expansion Card must not draw more than the specified current from any rail.
- Backplane implementations are not required to enforce those limits electrically.
- Protective devices and their selection, thresholds, and behavior remain implementation-specific.
- The independently switched identity supply and its 20 mA card-side budget remain in effect.

This decision clarifies the electrical contract established by [0010 — Expansion Power Rails](0010-expansion-power-rails.md) and [0017 — Identity Rail 20 mA Current Limit](0017-identity-rail-current-limit.md).
