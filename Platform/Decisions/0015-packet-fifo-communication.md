# 0015 — Packet FIFO Communication for Low-Level Drivers

**Status:** Accepted

## Decision

Communication between an Expansion Slot's Low-Level-Driver Cog and the Southbridge Management Cog uses in-memory packet FIFOs implemented as ring buffers.

Each Expansion Slot may use up to eight FIFO channels in total:

- 0..4 upstream FIFOs: Low-Level-Driver → Management Cog
- 0..4 downstream FIFOs: Management Cog → Low-Level-Driver

Each FIFO is semantically independent. A Low-Level-Driver may use any number of upstream and downstream FIFOs within these limits, including none at all.

If a driver needs more independent logical channels than the available FIFOs provide, it must multiplex those logical channels in software.

The Mainboard does not communicate directly with the Low-Level-Driver interface. The Management Cog forwards data between the Mainboard-facing interface and the per-Slot Low-Level-Driver interface.

The concrete packet-ring representation and synchronization protocol are specified in [Low Level Drivers.md](../Low%20Level%20Drivers.md#packet-fifo-design).

## Rationale

In-memory FIFOs/ring buffers are a common way to communicate between CPUs that share memory.

Keeping Low-Level-Driver communication entirely in memory avoids requiring separate bus or I/O arbitration for every driver. Every Low-Level-Driver sees the same communication interface regardless of the physical protocol it implements.

This also decouples the Mainboard Slot interface from the Low-Level-Driver interface. The Mainboard-facing transport can change independently from the per-Slot Low-Level-Driver ABI as long as the Management Cog continues to translate between them.

Allowing up to eight FIFOs provides a useful symmetry with the eight GP pins available to each Expansion Slot. The FIFOs are not bound to pins or to any other fixed semantic role, but the upper limit is sufficient to conceptually dedicate one FIFO per pin when that is useful.

The upstream and downstream FIFOs remain fully independent. A driver only allocates the channels it actually needs.

### Why Packets Instead of Byte Streams

The original design used stream/byte-oriented FIFOs. This exposed a critical limitation: there is no natural message boundary.

Many protocols therefore had to recreate packet or transaction boundaries on top of the byte stream.

Packet-oriented FIFOs preserve those boundaries directly and can also reduce host-side processing for naturally stream-oriented protocols.

For example, a UART Low-Level-Driver may bundle approximately 1 ms of received bytes into one packet instead of forwarding every byte individually.

UART-style interfaces also benefit from keeping control-flow changes and transmitted data in the same ordered packet stream. Operations such as:

```text
set(RTS)
write(20 bytes)
clear(RTS)
```

can be queued in order rather than sending RTS changes out-of-band from the data.

This avoids ordering failures seen with some USB-serial style interfaces, where the hardware can effectively observe:

```text
set(RTS)
write(2 bytes)
clear(RTS)
write(18 bytes)
```

even though software requested the control changes around the complete write.

## Consequences

- Low-Level-Driver communication does not require per-driver external bus or I/O arbitration.
- All Low-Level-Drivers use the same in-memory communication model.
- Mainboard Slot transport details are isolated from the Low-Level-Driver ABI.
- Packet boundaries are preserved by the communication mechanism.
- Data and control operations can share one ordered message stream.
- Each Expansion Slot may use between zero and eight FIFOs in total, with up to four in either direction.
- Every FIFO is semantically independent.
- Drivers only allocate the FIFO channels they need.
- Drivers that require more logical channels than the available FIFOs must multiplex them in software.
