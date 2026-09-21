# 0006 — Default Mainboard Hardware

**Status:** Accepted

## Decision

The Default Mainboard uses the following core hardware:

- an RP2350B as the system CPU
- 8 MiB of external system RAM
- 10/100 Ethernet
- an integrated debug probe
- a four-port USB hub

The USB hub exposes its four downstream host ports as:

- USB0 on the Backplane
- USB1 on the Backplane
- one internal USB-A connector intended primarily for USB mass-storage devices
- one front-panel USB-A connector

## Rationale

This provides a complete first Mainboard implementation with networking, debugging, removable storage, front-panel USB, and the two Platform Mainboard USB host ports without requiring separate external development hardware.

## Consequences

- The Default Mainboard requires a four-port USB hub.
- Two hub ports are dedicated to the Mainboard-slot USB0/USB1 connections.
- One USB-A connector is available inside the computer.
- One USB-A connector is exposed on the front panel.
- More specific component selections are documented in separate decisions.
