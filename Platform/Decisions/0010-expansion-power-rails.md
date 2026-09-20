# 0010 — Expansion Power Rails

**Status:** Accepted

## Decision

The Expansion Bus provides three power rails to every Expansion Card:

- 3.3 V at up to 500 mA
- 5 V at up to 500 mA
- 12 V at up to 500 mA

The 12 V rail exists to support higher-power Expansion Cards that cannot be served reasonably by only the 3.3 V and 5 V rails.

## Rationale

The original design provided only 3.3 V and 5 V.

A 12 V rail was added for higher-power applications. With the current current limits, an Expansion Card can draw roughly 10 W across the three rails.

Across the complete computer this puts the intended system power budget in the neighborhood of 80 W.

Earlier designs allowed more current per rail. The present limits were reduced because the Backplane already has to distribute roughly 4 A per rail at system scale, which is a substantial current for the wiring, connectors, protection, and power distribution.

## Consequences

- Expansion Cards may rely on 3.3 V, 5 V, and 12 V being available.
- Each rail is limited to 500 mA per Expansion Card.
- The Backplane power-distribution design must account for approximately 4 A per rail at full system scale.
- Detailed rail tolerances, sequencing, inrush, protection behavior, and hot-swap behavior remain separate electrical-specification topics.
