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

The internal layout of each 64 KiB Cog Data area is otherwise defined by that Cog's Low-Level-Driver. FIFO storage must be allocated inside the associated Cog Data area and use the packet-ring representation specified below, but has no additional placement requirement. FIFO configuration and state are stored in the associated Cog Config area.

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

The concrete ring-buffer representation and synchronization protocol are specified in [Packet FIFO Design](#packet-fifo-design).

### FIFO Memory

FIFO storage for an Expansion Slot must be allocated from the associated Cog Data area.

The exact placement of FIFO storage within that 64 KiB area is defined by the Low-Level-Driver. FIFO storage uses the packet-ring representation specified below. FIFO configuration and state are stored in the associated Cog Config area.

## Packet FIFO Design

Each FIFO is a single-producer/single-consumer packet ring. The producer and consumer are fixed by the FIFO direction:

- for an upstream FIFO, the Low-Level-Driver is the producer and the Management Cog is the consumer
- for a downstream FIFO, the Management Cog is the producer and the Low-Level-Driver is the consumer

The design uses no hardware locks. The producer exclusively updates the commit sequence and the consumer exclusively updates the release sequence.

### FIFO Descriptor and State

Each FIFO uses two 32-bit values in the associated Cog Config area:

```text
configuration:
    bits 15..0   data offset
    bits 31..16  ring mask

state:
    bits 15..0   commit sequence
    bits 31..16  release sequence
```

The data offset is a 16-bit offset relative to the associated Cog Data base, not a full Hub RAM address.

FIFO storage sizes must be powers of two from 512 bytes through 32 KiB. The ring mask is therefore:

```text
ring_mask = ring_size - 1
```

The FIFO storage must fit completely inside the associated 64 KiB Cog Data area and must be word-aligned.


The sequence counters are 16-bit byte positions that wrap naturally modulo `2^16`. Because the largest FIFO is 32 KiB, the distance between commit and release is always unambiguous:

```text
used = (commit_sequence - release_sequence) & 0xFFFF
free = ring_size - used

physical_offset = sequence & ring_mask
```

The producer may read both sequence counters with one 32-bit load. Updates must modify only the owned 16-bit counter; no read-modify-write operation on the complete state long is required.

### Packet Representation

Packets are stored directly in the FIFO data ring:

```text
+------------------+----------------------+------------------+
| u16 payload_size | payload_size bytes   | optional padding |
+------------------+----------------------+------------------+
```

`payload_size` is in the range `1..2048`.

Each record is padded to a two-byte boundary:

```text
record_size = align2(2 + payload_size)
```

A payload size of zero is reserved as a **WRAP marker** and never represents a packet.

A packet must be stored contiguously. If the complete packet record does not fit between the current producer position and the physical end of the ring, the producer writes a WRAP marker at the current position, skips the remaining tail, and stores the packet at offset zero.

The skipped tail is part of the occupied logical sequence range until the consumer passes it. A record whose size is larger than the FIFO itself cannot be allocated.

### Producer Operations

`tryAllocate(size)` computes the occupied and free byte counts from the two sequence counters.

If the packet fits without wrapping, it tentatively writes the size header at:

```text
data_base + (commit_sequence & ring_mask)
```

If the packet requires wrapping, it tentatively writes a WRAP marker at the current position, advances the tentative position to the next ring boundary, and writes the packet header at `data_base`.

The allocation succeeds only if the complete logical space requirement fits in the available space. For a wrapped packet this includes both the skipped tail and the packet record.

The returned payload pointer addresses the bytes immediately following the packet header. Allocation does not modify the shared commit sequence, so the packet remains invisible to the consumer.

`write()` modifies only the tentatively allocated payload.

`commit()` starts again from the currently published commit sequence, reads the packet header, follows a WRAP marker when present, derives the complete record size, and advances the commit sequence past the packet. Publishing the new commit sequence makes the packet visible to the consumer.

Because the packet header contains all information required to reconstruct the committed range, no per-FIFO pending allocation state is required in Cog RAM.

`abort()` does not modify shared state. Any tentatively written WRAP marker, packet header, or payload remains outside the committed range and may be overwritten by the next allocation.

### Consumer Operations

`tryDequeue()` compares the commit and release sequences. Equal values indicate an empty FIFO.

Otherwise, the consumer reads the header at:

```text
data_base + (release_sequence & ring_mask)
```

If the header is a WRAP marker, the tentative release position advances to the next ring boundary and the packet header is read at `data_base`.

The operation returns the payload pointer and payload size without modifying the shared release sequence.

`read()` accesses the returned payload while the packet remains owned by the consumer.

`release()` starts again from the currently published release sequence, follows a WRAP marker when present, derives the packet record size, and advances the release sequence past the packet. Publishing the new release sequence makes that storage reusable by the producer.

As with the producer side, no per-FIFO pending dequeue state is required in Cog RAM.

### Concurrency and Ownership

Each sequence counter has exactly one writer:

- the producer writes only the commit sequence
- the consumer writes only the release sequence

Packet contents are written before the producer publishes the corresponding commit sequence. Packet storage is not reused until the consumer publishes the corresponding release sequence.

There may be at most one outstanding allocation on a given FIFO and at most one outstanding dequeue on a given FIFO. Operations on different FIFOs are independent, so all four upstream FIFOs and all four downstream FIFOs may have outstanding operations concurrently without requiring per-FIFO transaction state in Cog RAM.

## Management Cog Bootstrap

Propeller 2 bootstrap initially starts execution in **Cog 0**.

During Southbridge bootstrap, the Management Cog firmware must relocate its execution from Cog 0 to **Cog 7**.

After this relocation:

- Cog 7 is the Southbridge Management Cog.
- Cog 0 becomes available for Expansion Slot 0.
- Cogs 0..6 are available for the seven Low-Level-Drivers.

