# 0011 — Propeller 2 Resource Partition per Expansion Port

**Status:** Accepted

## Decision

Partition the Propeller 2 resources uniformly by Expansion Card port.

The Propeller 2 has 64 I/O pins. Seven Expansion Card ports each receive eight consecutive pins, and the final eight pins are allocated to the Mainboard/upstream interface:

| Consumer | P2 pins |
| --- | --- |
| Expansion 0 | P0..P7 |
| Expansion 1 | P8..P15 |
| Expansion 2 | P16..P23 |
| Expansion 3 | P24..P31 |
| Expansion 4 | P32..P39 |
| Expansion 5 | P40..P47 |
| Expansion 6 | P48..P55 |
| Mainboard / upstream | P56..P63 |

The Mainboard allocation must contain the Propeller 2 bootstrapping pins. This allows the Mainboard to control the bootstrapping/strapping pins and load the Southbridge firmware into the Propeller 2 during system startup.

Propeller 2 Cogs 0..6 are allocated one-to-one to Expansion ports 0..6.

Hub RAM is partitioned into 64 KiB regions starting at address 0. For Expansion port `n`, where `n` is also the Cog ID:

```text
pin_base = n * 8
ram_base = n * 64 KiB
```

This allows an Low-Level-Driver to derive its pin range and Hub RAM range directly from its Cog ID.

## Rationale

A uniform resource partition makes every Expansion port structurally equivalent and avoids per-port configuration tables.

The same Low-Level-Driver firmware can therefore be loaded for different ports and compute its own resources from `COGID`.

Placing the Mainboard/upstream group last keeps the Propeller 2 bootstrapping pins on the Mainboard side. This is required so the Mainboard can select the Propeller 2 bootstrap mode through the strapping pins and then load the Southbridge firmware. Exposing those pins through an Expansion Card port would prevent the Mainboard from performing any bootstrap.

## Consequences

- Every Expansion port receives eight contiguous Propeller 2 I/O pins.
- Every Expansion-port GP lane is backed directly by a Propeller 2 pin and has the full Smart Pin capability of that pin, subject to the Platform electrical limits.
- Cogs 0..6 correspond directly to Expansion ports 0..6.
- Each Expansion port has a deterministic 64 KiB Hub RAM region derived from its Cog ID.
- The Mainboard/upstream interface occupies P56..P63.
- The Propeller 2 bootstrapping/strapping pins are part of the Mainboard allocation so the Mainboard can load the Southbridge firmware.

The normative Low-Level-Driver execution model and resource mapping are documented in [Low Level Drivers.md](../Low%20Level%20Drivers.md).
