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

### Packet FIFO Operation Sequencing

Replace the prose-only description of packet FIFO producer and consumer operations with precise pseudocode.

The pseudocode should define the complete sequencing and failure behavior for:

- producer allocation, wrapping, payload access, commit, and abort
- consumer dequeue, wrapping, payload access, release
- free-space validation before any producer write that could overlap consumer-owned storage
- reconstruction of tentative positions from the published sequence counters
- ownership and visibility transitions
- the one-outstanding-operation-per-FIFO constraint
- whether explicit wakeup or notification signaling is required in addition to the shared FIFO state

The goal is to make the exact algorithm unambiguous without changing the existing FIFO format or concurrency model.

### Propeller 2 Cog Configuration Block Layout

Each Cog has a 2048-byte configuration block in the Hub RAM memory map.

Still to define:

- internal binary layout and field offsets
- FIFO descriptor placement, enumeration, and channel metadata
- representation of other per-Cog or per-Slot properties
- reserved fields and versioning/evolution rules

### Southbridge Management Cog Bootstrap Relocation

The Southbridge Management Cog starts in Cog 0 during Propeller 2 bootstrap and must end up running in Cog 7 before Cog 0 is used for Expansion Slot 0.

Still to define:

- the exact relocation/startup mechanism
- what state must be transferred or reconstructed when Cog 7 starts
- when Cog 0 becomes safe to reuse for the Expansion Slot 0 Low-Level-Driver

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

## Computer

### Default Mainboard ENC624J600 Host Interface

The Default Mainboard uses an ENC624J600 Ethernet controller, but the host-side protocol is not yet selected.

Still to define:

- whether the RP2350 communicates with the ENC624J600 over SPI or the parallel host interface
- the concrete bus width and signaling mode if the parallel interface is selected
- the resulting RP2350 pin assignment and timing requirements
- the throughput target that drives the selection

### Default Mainboard Flash Selection

The Default Mainboard is intended to provide 16 MiB of non-volatile flash storage, but the concrete flash device is not yet selected.

Still to define:

- exact flash part
- electrical interface and timing requirements
- erase/program characteristics relevant to firmware and storage layout
- any performance or availability constraints that affect the selection
