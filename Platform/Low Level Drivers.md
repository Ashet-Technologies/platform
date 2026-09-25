# Low Level Drivers

A **Low-Level-Driver** is the piece of firmware running on the Propeller 2 Cog associated with an Expansion Slot.

It acts as a **packet-to-card translator** between the Southbridge Management Cog and the physical Expansion Card interface. It implements the Southbridge-side handling of the slot's `GP0..GP7` signals and translates packet/datagram traffic into the card-specific electrical protocol.

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


### Hub RAM Memory Map

The complete 512 KiB Propeller 2 Hub RAM address space is partitioned as follows:

| Address range | Size | Purpose |
| --- | ---: | --- |
| `0x00000..0x0FFFF` | 64 KiB | Expansion Slot 0 |
| `0x10000..0x1FFFF` | 64 KiB | Expansion Slot 1 |
| `0x20000..0x2FFFF` | 64 KiB | Expansion Slot 2 |
| `0x30000..0x3FFFF` | 64 KiB | Expansion Slot 3 |
| `0x40000..0x4FFFF` | 64 KiB | Expansion Slot 4 |
| `0x50000..0x5FFFF` | 64 KiB | Expansion Slot 5 |
| `0x60000..0x6FFFF` | 64 KiB | Expansion Slot 6 |
| `0x70000..0x70FFF` | 4 KiB | Cog 0 program/configuration block |
| `0x71000..0x71FFF` | 4 KiB | Cog 1 program/configuration block |
| `0x72000..0x72FFF` | 4 KiB | Cog 2 program/configuration block |
| `0x73000..0x73FFF` | 4 KiB | Cog 3 program/configuration block |
| `0x74000..0x74FFF` | 4 KiB | Cog 4 program/configuration block |
| `0x75000..0x75FFF` | 4 KiB | Cog 5 program/configuration block |
| `0x76000..0x76FFF` | 4 KiB | Cog 6 program/configuration block |
| `0x77000..0x77FFF` | 4 KiB | Cog 7 program/configuration block |
| `0x78000..0x7BFFF` | 16 KiB | Unallocated |
| `0x7C000..0x7FFFF` | 16 KiB | Reserved |

Each Cog program/configuration block is 4096 bytes and is split evenly:

- the first 2048 bytes are the Cog program slot
- the second 2048 bytes are the Cog configuration block

For Cog `n`:

```text
cog_block_base = 0x70000 + n * 0x1000
program_base   = cog_block_base
config_base    = cog_block_base + 0x800
```

The final 16 KiB at `0x7C000..0x7FFFF` is reserved as one opaque block.

## Southbridge Management Cog

The **Southbridge Management Cog** is the Propeller 2 Cog responsible for managing the Low-Level-Drivers and their communication with the rest of the Southbridge.

After bootstrap, the Southbridge Management Cog runs in **Cog 7**.

### Communication Channels

Each Expansion Slot may use up to eight packet FIFO channels between its Low-Level-Driver and the Southbridge Management Cog:

- **0..4 upstream FIFOs:** Low-Level-Driver → Southbridge Management Cog
- **0..4 downstream FIFOs:** Southbridge Management Cog → Low-Level-Driver

Each FIFO is an independent ring buffer carrying discrete datagrams/packets and is semantically independent from every other FIFO.

A Low-Level-Driver may use any number of upstream and downstream FIFOs within these limits, including zero FIFOs. If more logical channels are required than the available FIFOs provide, they must be multiplexed in software.

Each packet has a payload size of:

```text
1..2048 bytes
```

The exact ring-buffer representation, packet framing, signaling, queue depth, and synchronization rules are not yet specified.

### FIFO Memory

All FIFO storage for an Expansion Slot is allocated from that Slot's **64 KiB Hub RAM region**.

FIFO memory therefore consumes part of the same per-slot 64 KiB region available to the Low-Level-Driver.

### Shared Memory Access

The Southbridge Management Cog may directly read and write the complete 64 KiB Hub RAM region assigned to each Expansion Slot.

This memory therefore also acts as shared memory between the Low-Level-Driver and the Southbridge Management Cog.

No additional copying mechanism is required for data that both sides agree to exchange through shared memory.

The ownership, synchronization, and consistency rules for shared-memory data are not yet specified.

## Management Cog Bootstrap

Propeller 2 bootstrap initially starts execution in **Cog 0**.

During Southbridge bootstrap, the Management Cog firmware must relocate its execution from Cog 0 to **Cog 7**.

After this relocation:

- Cog 7 is the Southbridge Management Cog.
- Cog 0 becomes available for Expansion Slot 0.
- Cogs 0..6 are available for the seven Low-Level-Drivers.

The exact relocation mechanism is not yet specified.
