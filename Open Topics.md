# Open Topics

This document tracks high-level Platform and Computer design areas that are intentionally not yet specified.

## Platform

### Mainboard ↔ Backplane Interface

Still to define:

- the exact electrical timing requirements for sampling `/FAB_RESET`
- behavior if `/FAB_RESET` changes after the initial startup sample
- reset and recovery behavior after faults
- remaining timing requirements for mode transitions and interface enablement
- any additional electrical limits not already covered by the connector specifications
- firmware-defined transport/protocol constraints outside the defined Not-so-mainboard bootstrap behavior

### Not-so-mainboard Reset and Initialization Sequence

The current Expansion Slot activation sequence asserts `/RESET` before power-on and reads the Expansion Card EEPROM while `/RESET` remains asserted.

A Not-so-mainboard must nevertheless emulate that EEPROM while installed in an Expansion Slot. A hard reset driven directly by `/RESET` may therefore prevent the EEPROM emulation required for discovery.

Reconsider the initialization/reset sequence. In particular, define:

- whether a Not-so-mainboard must soft-sample `/RESET` instead of using it as a hard reset input
- whether the Backplane must release `/RESET` before EEPROM discovery for Not-so-mainboards
- how reset semantics apply after the Not-so-mainboard Low-Level-Driver is active
- whether the current requirement that a card enter a power-on-equivalent safe state while `/RESET` is asserted needs a Not-so-mainboard-specific exception

### Standard HSTX Pinouts

Interfaces requiring definitions include:

- DVI
- QSPI
- QPI
- MIPI-DSI, 1 lane
- MIPI-DSI, 2 lanes
- MIPI-CSI


### Expansion Card Mechanical Constraints

Still to define:

- connector keying requirements
- card outline
- height limits
- retention details

### Expansion Power Electrical Details

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

- the seven 64 KiB Expansion Slot Cog regions
- Management Cog memory
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
- preserve the same driver binary/source model across Expansion Slots
- determine whether relocation metadata, patching, or another lightweight linking mechanism is appropriate

### Backplane I²C Subnet

The PCA9547 provides eight downstream I²C channels. Seven channels are assigned one-to-one to the seven Expansion Slots. The eighth channel forms the Backplane-local system-management I²C subnet.

The connected device classes and address assignments are defined in [Platform/System Management I2C.md](Platform/System%20Management%20I2C.md).

Still to define:

- bus speed, pull-ups, and power-domain behavior
- how the Mainboard discovers and initializes devices on the subnet
- reset and failure behavior for Backplane-local devices

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

### Expansion Card EEPROM Versioning

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

Still to define:

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

Document the historical/design background of the format, including the exploration that led to the encoding and why it was selected over alternative 8-bit color representations.

## Computer

### Default Mainboard Flash Selection

The Default Mainboard is intended to provide 16 MiB of non-volatile flash storage, but the concrete flash device is not yet selected.

Still to define:

- exact flash part
- electrical interface and timing requirements
- erase/program characteristics relevant to firmware and storage layout
- any performance or availability constraints that affect the selection
