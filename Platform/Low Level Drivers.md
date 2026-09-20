# Low Level Drivers

A **Low-Level-Driver** is the piece of firmware running on the Propeller 2 Cog associated with an Expansion Card Slot.

It implements the Southbridge-side handling of the slot's `GP0..GP7` signals. HSTX signals are not controlled by the Low-Level-Driver; they are handled by the regular Expansion Card driver and the selected HSTX interface definition.

## Southbridge Resource Partition

The Propeller 2 resources are partitioned uniformly across the seven Expansion ports:

| Expansion port | Cog | P2 pins | Hub RAM |
| ---: | ---: | --- | --- |
| 0 | 0 | P0..P7 | 0 KiB..64 KiB |
| 1 | 1 | P8..P15 | 64 KiB..128 KiB |
| 2 | 2 | P16..P23 | 128 KiB..192 KiB |
| 3 | 3 | P24..P31 | 192 KiB..256 KiB |
| 4 | 4 | P32..P39 | 256 KiB..320 KiB |
| 5 | 5 | P40..P47 | 320 KiB..384 KiB |
| 6 | 6 | P48..P55 | 384 KiB..448 KiB |

For a Low-Level-Driver running in Cog `n`:

```text
pin_base = n * 8
ram_base = n * 64 KiB
```

The driver can therefore derive its GP pin range and Hub RAM region directly from its Cog ID.

Each GP lane is backed directly by a Propeller 2 I/O pin and exposes the full Smart Pin capability of that pin, subject to the electrical limits of the Expansion Bus.

The resource partition is established by [Decision 0011](Decisions/0011-propeller2-port-resource-partition.md).
