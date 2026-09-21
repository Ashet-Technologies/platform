# Open Topics

This document tracks Platform and Computer design areas that are intentionally not yet specified.

## Platform

### Mainboard ↔ Southbridge Bootstrap and Transport

The physical Mainboard interface and connector semantics are defined, and the Mainboard is responsible for loading Southbridge firmware into the Propeller 2.

Still to define:

- the exact Southbridge bootstrap/loading sequence
- timing requirements during bootstrap
- the runtime Mainboard ↔ Southbridge transport/protocol
- error handling and recovery for the transport

### Not-so-mainboard Mode

A Mainboard can detect that it is installed in an Expansion Slot through A8/`/FAB_RESET`. When `/FAB_RESET` is high during startup, normal Mainboard operation must not begin.

Still to define:

- the exact point during startup when `/FAB_RESET` is sampled
- whether the Card enters a fully high-impedance state first or directly enters Not-so-mainboard mode
- which signals must be high-impedance during detection and transition
- discovery and command/task protocol
- lifecycle and error handling
- behavior if `/FAB_RESET` changes after startup
- reset and recovery behavior

### Standard HSTX Pinouts

The HSTX interface model and standard interface classes are defined, but the exact standard pin mappings and detailed interface requirements are not.

Interfaces requiring definitions include:

- DVI
- QSPI
- QPI
- MIPI-DSI, 1 lane
- MIPI-DSI, 2 lanes
- MIPI-CSI

### Expansion Card Mechanical Constraints

The PCI Express x4 connector is fixed, but the remaining mechanical envelope is not yet specified.

Still to define:

- connector keying requirements
- card outline
- height limits
- retention details

### Expansion Power Electrical Details

The available rails, 500 mA per-rail limit, synchronous rail switching, and eFuse requirement are defined.

Still to define:

- voltage tolerances
- inrush limits
- transient behavior
- detailed overcurrent behavior
- connector/power-distribution compliance requirements

### Hot-Swap Contract

Hot-swap support is optional.

For implementations that provide it, still define:

- insertion/removal sequencing
- power behavior
- reset behavior
- signal high-impedance requirements
- presence-detection timing
- failure behavior

### Standard Low-Level Driver Interfaces

The EEPROM can select a Platform-standard Low-Level-Driver interface instead of embedding card-specific firmware.

Still to define:

- interface identifiers
- standard interfaces
- Southbridge-side ABI
- host-side ABI
- lifecycle and error behavior

### Propeller 2 Memory Map

Fully define the Southbridge Propeller 2 Hub RAM memory map.

This includes:

- the seven 64 KiB Expansion Slot Cog regions
- Management Core memory
- FIFO allocations inside each Expansion Slot region
- shared-memory regions
- firmware/code placement
- reserved or global Southbridge memory

### Low-Level-Driver Relocation

Low-Level-Drivers currently derive their GP pin base and Hub RAM base from their Cog ID at runtime.

Investigate whether drivers should instead be relocated or patched when loaded so these addresses can be resolved ahead of execution.

### Low-Level-Driver Communication ABI

The basic packet-FIFO and shared-memory model is defined, but its concrete ABI is not.

Still to define:

- ring-buffer representation
- packet framing
- signaling
- queue depth
- synchronization
- shared-memory ownership and consistency rules
- Management Core bootstrap relocation details

### Backplane I²C Subnet

The PCA9547 provides eight downstream I²C channels. Seven channels are assigned one-to-one to the seven Expansion Slots. The eighth channel is intended to form a Backplane-local I²C subnet.

Still to define:

- which Backplane devices are connected to this subnet
- I²C address assignments
- bus speed
- pull-ups and power-domain behavior
- Mainboard discovery and initialization
- reset and failure behavior

### Expansion I²C Speed Declaration

The Expansion Bus runs nominally at 100 kHz and may run at up to 400 kHz when the Card supports it.

Still to define:

- how a Card declares its maximum supported I²C speed
- when the Mainboard may change the bus speed
- fallback behavior when devices on the same Card have different limits

### Audio Data-Lane Direction

The Audio bus is always I²S. Both data lanes may be bidirectional when the Mainboard and Expansion Card hardware support it. Default directions are already defined.

Still to define:

- how Mainboards and Expansion Cards declare bidirectional data-lane capability
- how direction changes are negotiated and applied
- electrical requirements while changing direction
- behavior when a requested Audio profile is unsupported

### Shared Audio Clocks Across Cards

Investigate distributing `MCLK`, `BCLK`, and `WCLK` as shared system-wide audio clocks even to Slot positions without Audio data lanes.

Goal: deterministic audio synchronization between multiple Expansion Cards.

### Expansion Card EEPROM Versioning

Metadata version 1 is defined.

Still to define:

- handling of unknown versions
- rules for future metadata-version evolution
- feature negotiation
- rejection/fallback behavior

### EEPROM Cross-Validation

Define validation rules between related EEPROM fields.

Examples include:

- `Requires High-Speed` versus `High-Speed Profile`
- `Requires Audio` versus `Audio Profile`
- `Requires Clock` versus interfaces that depend on `CLK`
- `Has Firmware` versus `Driver Interface`
- behavior for unsupported requested profiles

### Platform Electrical Compliance

Nominal levels and frequency limits are defined, but a general electrical compliance specification is still missing.

Still to define:

- logic thresholds
- drive strength
- load/capacitance limits
- slew rate
- analog constraints
- differential-signaling constraints

### Platform Revision Policy

There is no formal Platform revision scheme yet.

Still to define:

- Platform version identifier
- rules for future Platform revisions
- reserved-field evolution
- feature negotiation

## Computer

### Default Mainboard Flash Selection

The Default Mainboard is intended to provide 16 MiB of non-volatile flash storage, but the concrete flash device is not yet selected.

Still to define:

- exact flash part
- electrical interface and timing requirements
- erase/program characteristics relevant to firmware and storage layout
- performance and availability constraints that affect the selection
