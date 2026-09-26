# 0016 — Screwless Expansion Card Retention

**Status:** Accepted

## Decision

Expansion Cards are installed **without retention screws** in the Ashet Home Computer mechanical design.

## Rationale

Retention screws were originally planned for the Expansion Card design.

Removing them frees **10 mm of vertical card-slot space**, which can instead be used for additional or larger external connectors.

The PCI Express x4 connector already holds an Expansion Card tightly enough that removing a card by pulling it directly from the connector is not easy. The Expansion Card Slot guide rails make the installed fit tighter still.

Because the connector and guide rails provide sufficient retention for the intended design, separate screw fastening is not necessary.

## Consequences

- Expansion Cards do not require screw-retention hardware or corresponding mounting points.
- The available vertical card-slot space increases by 10 mm.
- Expansion Card designs have more space available for external connectors.
- Card retention relies on the PCI Express x4 connector together with the Expansion Card Slot guide rails.
