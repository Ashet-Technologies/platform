# 0014 — Conditional Per-Slot Clock

**Status:** Accepted

## Decision

Expansion Card metadata contains a `Requires Clock` property.

When `Requires Clock` is set, the slot's 48 MHz `CLK` signal is enabled and stable before `/RESET` is released.

When `Requires Clock` is clear, the Backplane may mute the slot clock.

## Rationale

A permanently driven 48 MHz clock creates an otherwise unnecessary high-frequency stub for cards that do not use it.

Allowing the clock to be disabled per slot reduces EMI and unnecessary switching activity while preserving a deterministic clock source for cards that require it.

## Consequences

- `Requires Clock` occupies the lowest previously-reserved property bit.
- Cards that require `CLK` for correct operation must set `Requires Clock`.
- Mainboard software must enable the clock early enough that it is stable before reset release.
- Cards that do not require `CLK` must not rely on the signal being present.
