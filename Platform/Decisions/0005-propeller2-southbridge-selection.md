# 0005 — Propeller 2 as the Southbridge

**Status:** Accepted

## Context

The original expansion architecture exposed a fixed set of interfaces to every Expansion Card:

- one I²C bus
- one USB 1.1 interface
- one SPI bus with two chip-select signals

This made the electrical design comparatively expensive while still restricting what an Expansion Card could do. Each slot required dedicated interface hardware and routing, and cards that needed behavior outside that fixed set had little flexibility.

## Decision

Use the **Propeller 2** as the platform Southbridge and make its programmable I/O resources the basis of the Expansion Card interface.

The Propeller 2 was originally selected for two main reasons:

1. **Uniform, unusually capable I/O.** Its programmable I/O and processing resources allow each Expansion Card interface to be implemented in software rather than being fixed to a small set of hard-wired peripheral controllers.
2. **Lower total system cost.** The Backplane becomes somewhat more expensive, but Expansion Cards become substantially cheaper and simpler.

The Propeller 2 can provide interfaces such as I²C, SPI, and USB 1.1 to Expansion Cards as **software-defined interfaces**. The exact interface behavior is therefore not restricted to a fixed peripheral mix.

## Virtual MCU per Expansion Port

A useful architectural model is that the Southbridge provides an effective **virtual MCU** for each of the seven Expansion Card ports.

For each port, the platform can effectively dedicate:

- one Propeller 2 CPU core
- approximately 64 KiB of RAM
- FIFO-based upstream and downstream communication with the Southbridge Management Core, which forwards data between the Expansion Cog and the Mainboard

These are an allocation model of the shared Propeller 2 resources, not physically separate MCUs.

This lets the Southbridge absorb protocol handling and low-level control logic that would otherwise have to be implemented on every Expansion Card.

## Example

A four-port PS/2 Expansion Card can be implemented primarily as passive support circuitry:

- 8 level-shifter transistors
- 16 resistors
- 1 I²C metadata EEPROM

Without the Propeller 2 Southbridge, the same card would require an additional MCU to implement the PS/2 protocol and communicate with the host.

With the Southbridge architecture, that MCU-like role is handled centrally by the Propeller 2.

## Consequences

- Expansion Cards can be significantly cheaper and simpler.
- Many Expansion Cards can consist mostly of connectors, level shifting, analog circuitry, and the mandatory metadata EEPROM.
- Protocol implementation can live in Southbridge firmware instead of requiring an MCU on every card.
- The Backplane carries more cost and complexity in exchange for reducing the cost and complexity of every Expansion Card.
- Expansion interfaces can evolve in software while remaining inside the electrical and timing constraints of the Platform specification.
- The seven Expansion Card ports can be treated as having similar computational support from the Southbridge, simplifying the overall expansion architecture.
