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

### Standard HSTX Pinouts

The HSTX interface model and standard interface classes are defined, but the exact standard pin mappings are not.

Interfaces requiring definitions include:

- DVI
- MIPI-DSI, 1 lane
- MIPI-DSI, 2 lanes

The Custom and Unused interface semantics are already defined at a high level.

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

### Low-Level Driver Dynamic Linking

Low-Level-Drivers currently derive their GP pin base and Hub RAM base from their Cog ID at runtime.

Investigate dynamically linking or relocating Low-Level-Drivers when they are loaded so these addresses can be resolved ahead of execution instead.

Goals include:

- avoid repeated runtime computation of pin and memory offsets
- allow direct use of resolved addresses in driver code
- preserve the same driver binary/source model across Expansion Card slots
- determine whether relocation metadata, patching, or another lightweight linking mechanism is appropriate

### Expansion Card EEPROM Versioning

Metadata version 1 is defined.

Still to define:

- handling of unknown versions
- backward/forward compatibility rules
- feature negotiation
- rejection/fallback behavior

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
- compatibility rules
- reserved-field evolution
- feature negotiation
- backward/forward compatibility expectations

### Ashet HSV Background

The Ashet HSV encoding and its Platform-wide use are specified.

The historical/design background of the format is still to be documented, including the exploration that led to the encoding and why it was selected over alternative 8-bit color representations.

## Computer

No additional high-level Computer-specific open topic is currently recorded beyond the Platform topics that affect the concrete implementation.
