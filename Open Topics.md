# Open Topics

This document tracks high-level Platform and Computer design areas that are intentionally not yet specified.

## Platform

### Mainboard ↔ Backplane Interface

The logical and electrical Mainboard↔Backplane interface beyond the connector pin allocation is not yet specified.

Open work includes:

- bootstrap sequence
- reset behavior
- timing requirements
- electrical limits
- ownership and direction of configurable signals
- firmware-defined transport/protocol constraints

### Not-so-mainboard Mode

The A8 detection mechanism is defined, but the behavior of a Mainboard when installed in an Expansion Card slot is not yet specified.

Open work includes:

- discovery
- boot behavior
- command/task interface
- lifecycle
- error handling
- interaction with the host Mainboard

### Fabric Reset Boot Behavior

Mainboards must sample `/FAB_RESET` during startup.

If `/FAB_RESET` is high at boot, the Mainboard must not enter normal Mainboard operation. It must instead enter either:

- a high-impedance state, or
- Not-so-mainboard mode

Still to define:

- the exact point during startup when `/FAB_RESET` is sampled
- whether high-impedance mode or Not-so-mainboard mode is selected automatically
- which signals must be high-impedance
- behavior if `/FAB_RESET` changes after the initial sample
- reset and recovery behavior

### Standard HSTX Pinouts

The HSTX interface model and standard interface classes are defined, but the exact standard pin mappings are not.

Interfaces requiring definitions include:

- DVI
- QSPI
- QPI
- MIPI-DSI, 1 lane
- MIPI-DSI, 2 lanes
- MIPI-CSI

The Custom and Unused interface semantics are already defined at a high level.

### Expansion Card Mechanical Constraints

The PCI Express x4 connector is fixed, but the remaining mechanical envelope is not yet specified.

Still to define:

- connector keying requirements
- card outline
- height limits
- retention details

### Expansion Power Electrical Details

The available rails and per-card current limits are defined.

Still to define:

- voltage tolerances
- sequencing
- inrush limits
- transient behavior
- overcurrent behavior
- connector/power-distribution compliance requirements

### Hot-Swap Contract

Hot-swap support is optional.

For implementations that provide it, the following still need definition:

- insertion/removal sequencing
- power behavior
- reset behavior
- signal high-impedance requirements
- presence detection timing
- failure behavior

### Standard Low-Level Driver Interfaces

The EEPROM can select a platform-standard low-level driver interface instead of embedding card-specific firmware.

Still to define:

- interface identifiers
- standard interfaces
- Southbridge-side ABI
- host-side ABI
- lifecycle and error behavior

### Propeller 2 Memory Map

Fully define the Southbridge Propeller 2 Hub RAM memory map.

This includes:

- the seven 64 KiB Expansion Cog regions
- Management Core memory
- FIFO allocations inside each Expansion region
- shared-memory regions
- firmware/code placement
- any reserved or global Southbridge memory

### Low-Level Driver Dynamic Linking

Low-Level-Drivers currently derive their GP pin base and Hub RAM base from their Cog ID at runtime.

Investigate dynamically linking or relocating Low-Level-Drivers when they are loaded so these addresses can be resolved ahead of execution instead.

Goals include:

- avoid repeated runtime computation of pin and memory offsets
- allow direct use of resolved addresses in driver code
- preserve the same driver binary/source model across Expansion Card slots
- determine whether relocation metadata, patching, or another lightweight linking mechanism is appropriate

### Expansion I²C Speed Declaration

The Expansion Bus is nominally operated at 100 kHz and may run at up to 400 kHz when the card supports it.

Still to define:

- how a card declares its maximum supported I²C bus speed
- when the Mainboard may change the bus speed
- fallback behavior when multiple devices on the card have different limits

### Audio Data-Lane Direction

The Audio bus is always I²S.

Both I²S data lanes may be used bidirectionally when the Mainboard and Expansion Card hardware support it. By default, `I2S_SDIN` carries Card → Mainboard data and `I2S_SDOUT` carries Mainboard → Card data.

Still to define:

- how Mainboards and Expansion Cards declare bidirectional data-lane capability
- how direction changes are negotiated and applied
- electrical requirements while changing direction
- required behavior when a requested Audio profile is unsupported

### Shared Audio Clocks Across Cards

Investigate distributing `MCLK`, `BCLK`, and `WCLK` as shared system-wide audio clocks even to card positions without I²S data lanes.

Goal: provide deterministic audio synchronization between multiple Expansion Cards.

### Activation Sequence Diagram

Add a Mermaid diagram for the Expansion Card activation sequence: assert reset, power on, read/validate EEPROM, optional clock setup, optional HSTX setup, optional Audio setup, Low-Level-Driver loading, Expansion Card Driver loading, then reset release.

### Standard Interface Terminology

The term **Standard Interface** can currently refer to either:

- an electrical interface/profile
- a software/driver interface

Define terminology that makes this distinction explicit.

### Packet FIFO Interface Rationale

Document the rationale for using packet/datagram-based FIFO ports between Low-Level-Drivers and the Southbridge Management Core.

The rationale should cover why message boundaries are useful compared with an unstructured byte stream and how the model interacts with shared memory.

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

### Expansion Card Lifecycle

The individual mechanisms exist, but the complete lifecycle is not yet specified as one contract.

Still to define:

- presence detection
- reset
- EEPROM discovery
- compatibility check
- driver selection/loading
- activation
- removal

### Platform Electrical Compliance

Nominal levels and several frequency limits are defined, but a general electrical compliance specification is still missing.

Potential items include:

- logic thresholds
- drive strength
- load/capacitance limits
- slew rate
- analog constraints
- differential signaling constraints

### Platform Revision and Compatibility Policy

There is no formal Platform revision scheme yet.

Still to define:

- Platform version identifier
- rules for future Platform revisions
- reserved-field evolution
- feature negotiation

### Ashet HSV Background

The Ashet HSV encoding and its Platform-wide use are specified.

The historical/design background of the format is still to be documented, including the exploration that led to the encoding and why it was selected over alternative 8-bit color representations.

## Computer

No additional high-level Computer-specific open topic is currently recorded beyond the Platform topics that affect the concrete implementation.
