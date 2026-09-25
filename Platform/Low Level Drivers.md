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
| `0x00000..0x0FFFF` | 64 KiB | Cog 0 Data |
| `0x10000..0x1FFFF` | 64 KiB | Cog 1 Data |
| `0x20000..0x2FFFF` | 64 KiB | Cog 2 Data |
| `0x30000..0x3FFFF` | 64 KiB | Cog 3 Data |
| `0x40000..0x4FFFF` | 64 KiB | Cog 4 Data |
| `0x50000..0x5FFFF` | 64 KiB | Cog 5 Data |
| `0x60000..0x6FFFF` | 64 KiB | Cog 6 Data |
| `0x70000..0x707FF` | 2 KiB | Cog 0 Code |
| `0x70800..0x70FFF` | 2 KiB | Cog 0 Config |
| `0x71000..0x717FF` | 2 KiB | Cog 1 Code |
| `0x71800..0x71FFF` | 2 KiB | Cog 1 Config |
| `0x72000..0x727FF` | 2 KiB | Cog 2 Code |
| `0x72800..0x72FFF` | 2 KiB | Cog 2 Config |
| `0x73000..0x737FF` | 2 KiB | Cog 3 Code |
| `0x73800..0x73FFF` | 2 KiB | Cog 3 Config |
| `0x74000..0x747FF` | 2 KiB | Cog 4 Code |
| `0x74800..0x74FFF` | 2 KiB | Cog 4 Config |
| `0x75000..0x757FF` | 2 KiB | Cog 5 Code |
| `0x75800..0x75FFF` | 2 KiB | Cog 5 Config |
| `0x76000..0x767FF` | 2 KiB | Cog 6 Code |
| `0x76800..0x76FFF` | 2 KiB | Cog 6 Config |
| `0x77000..0x777FF` | 2 KiB | Cog 7 Code |
| `0x77800..0x77FFF` | 2 KiB | Cog 7 Config |
| `0x78000..0x7BFFF` | 16 KiB | Unallocated |
| `0x7C000..0x7FFFF` | 16 KiB | Reserved |

For Cog `n`:

```text
data_base   = n * 0x10000
code_base   = 0x70000 + n * 0x1000
config_base = code_base + 0x800
```

The internal layout of each 64 KiB Cog Data area is entirely defined by that Cog's Low-Level-Driver. FIFO storage must be allocated inside the associated Cog Data area, but has no additional placement requirement. FIFO configuration is stored in the associated Cog Config area.

A Low-Level-Driver must only access its associated Cog Data and Cog Config areas. No additional ownership or partitioning rules are imposed on the contents of the Cog Data area.

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

FIFO storage for an Expansion Slot must be allocated from the associated Cog Data area.

The exact placement and representation of FIFO storage within that 64 KiB area are defined by the Low-Level-Driver. FIFO configuration is stored in the associated Cog Config area.

## Management Cog Bootstrap

Propeller 2 bootstrap initially starts execution in **Cog 0**.

During Southbridge bootstrap, the Management Cog firmware must relocate its execution from Cog 0 to **Cog 7**.

After this relocation:

- Cog 7 is the Southbridge Management Cog.
- Cog 0 becomes available for Expansion Slot 0.
- Cogs 0..6 are available for the seven Low-Level-Drivers.

The exact relocation mechanism is not yet specified.
