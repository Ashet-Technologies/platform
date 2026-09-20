# 0013 — Open-Collector Expansion Reset

**Status:** Accepted

## Decision

The Expansion Bus `/RESET` signal is an active-low **open-collector** signal.

The Backplane never drives `/RESET` high and does not provide a pull-up for the signal.

An Expansion Card that uses `/RESET` provides its own pull-up and may pull the signal up to any voltage up to **12 V**.

The Backplane only asserts reset by sinking the line to GND.

## Rationale

Allowing the card to choose the reset pull-up voltage lets devices with reset inputs outside the 3.3 V domain use the platform reset signal directly.

In particular, a card may use a 12 V reset level when required by its circuitry.

Keeping the Backplane side purely open-collector avoids imposing a voltage domain on the card and avoids unnecessary pull-up components for simple cards that do not use `/RESET`.

## Consequences

- `/RESET` is exempt from the normal 0..3.3 V signal-domain rule.
- The maximum permitted high level on `/RESET` is 12 V.
- Cards that use `/RESET` are responsible for providing the pull-up.
- The Backplane must tolerate the permitted pull-up voltage while sinking reset.
- Cards that do not use `/RESET` may leave it unconnected.
