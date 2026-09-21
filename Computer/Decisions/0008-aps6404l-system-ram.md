# 0008 — APS6404L-3SQR-SN System RAM

**Status:** Accepted

## Decision

Use the **APS6404L-3SQR-SN** as the external system RAM of the Default Mainboard.

It provides 8 MiB of QSPI PSRAM.

## Rationale

For this design, the APS6404L-3SQR-SN is effectively the only affordable 8 MiB QSPI PSRAM solution.

It is not an ideal performance match for the system, but its price is competitive enough that the performance trade-off is acceptable for the Default Mainboard.

## Consequences

- The Default Mainboard provides 8 MiB of external PSRAM.
- Software and hardware design must account for the latency and bandwidth limits of this PSRAM.
- A different Mainboard implementation may choose a different memory technology.
