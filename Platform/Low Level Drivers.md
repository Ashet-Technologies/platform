# Low Level Drivers

A **Low-Level-Driver** is the piece of firmware running on the Propeller 2 Cog associated with an Expansion Slot.

It acts as a **packet-to-card translator** between the Southbridge Management Core and the physical Expansion Card interface. It implements the Southbridge-side handling of the slot's `GP0..GP7` signals and translates packet/datagram traffic into the card-specific electrical protocol.

A Low-Level-Driver does **not** expose operating-system functionality by itself. OS-visible functionality is provided by the Expansion Card Driver running on the Mainboard.

HSTX signals are not controlled by the Low-Level-Driver; they are handled by the Expansion Card Driver and the selected HSTX interface definition.

## Southbridge Resource Partition

The Propeller 2 resources are partitioned uniformly across the seven Expansion Slots:

| Expansion Slot | Cog | P2 pins | Hub RAM |
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

The resource partition is established by [Decision 0011](Decisions/0011-propeller2-slot-resource-partition.md).

## Southbridge Management Core

The **Southbridge Management Core** is the Propeller 2 Cog responsible for managing the Low-Level-Drivers and their communication with the rest of the Southbridge.

After bootstrap, the Southbridge Management Core runs in **Cog 7**.

### Communication Channels

Each Expansion Slot may expose up to eight packet FIFO ports between its Low-Level-Driver and the Southbridge Management Core:

- **0..4 upstream FIFO ports:** Low-Level-Driver → Southbridge Management Core
- **0..4 downstream FIFO ports:** Southbridge Management Core → Low-Level-Driver

Each FIFO port is an independent ring buffer carrying discrete datagrams/packets.

Each packet has a payload size of:

```text
1..2048 bytes
```

The exact ring-buffer representation, packet framing, signaling, queue depth, and synchronization rules are not yet specified.

### FIFO Memory

All FIFO storage for an Expansion Slot is allocated from that slot's **64 KiB Hub RAM region**.

FIFO memory therefore consumes part of the same per-slot 64 KiB region available to the Low-Level-Driver.

### Shared Memory Access

The Southbridge Management Core may directly read and write the complete 64 KiB Hub RAM region assigned to each Expansion Slot.

This memory therefore also acts as shared memory between the Low-Level-Driver and the Southbridge Management Core.

No additional copying mechanism is required for data that both sides agree to exchange through shared memory.

The ownership, synchronization, and consistency rules for shared-memory data are not yet specified.

## Management Core Bootstrap

Propeller 2 bootstrap initially starts execution in **Cog 0**.

During Southbridge bootstrap, the management firmware must relocate its execution from Cog 0 to **Cog 7**.

After this relocation:

- Cog 7 is the Southbridge Management Core.
- Cog 0 becomes available for Expansion Slot 0.
- Cogs 0..6 are available for the seven Low-Level-Drivers.

The exact relocation mechanism is not yet specified.
