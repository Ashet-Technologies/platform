# 0010 — USB MSC as Primary Mass Storage

**Status:** Accepted

## Decision

Use **USB Mass Storage Class (USB MSC)** as the primary removable/in-system mass-storage interface of the Default Mainboard.

## Rationale

SD-card interfaces generally require more board- and software-specific handling.

USB MSC devices are inexpensive, widely available, and expose a standardized interface. USB-to-SD adapters also allow SD cards to be used when desired without making SD a native Mainboard storage interface.

## Consequences

- The internal USB-A connector is the primary intended location for in-system mass storage.
- Standard USB flash drives can be used directly.
- SD cards remain usable through USB-SD adapters.
- Native SD-card hardware is not required on the Default Mainboard.
