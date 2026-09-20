# platform

## Glossary

- **Backplane:** The interconnect between the Mainboard and Expansion Cards.
- **Southbridge:** The Propeller 2 on the Backplane dispatching data between the Mainboard and Expansion Cards. Provides the main electrical expansion interface.
- **Mainboard:** Contains the CPU and RAM of the system and runs the OS. Connects to the Backplane.
- **Expansion Card:** A generic expansion that provides additional, user-selectable features to the computer.
- **Not-so-mainboard:** A Mainboard that supports being plugged into an Expansion Card slot and can be used as a coprocessor of sorts. It accepts commands or tasks when used in an Expansion Card slot.
- **Low-Level-Driver:** Firmware running on the Propeller 2 Cog associated with an Expansion Card Slot. It translates packet/datagram traffic into the card-specific electrical protocol and does not directly provide OS functionality.
- **Expansion Card Driver:** Driver code running on the Mainboard that exposes an Expansion Card's functionality to the Mainboard operating system. It communicates with the card through the Southbridge/Low-Level-Driver path and controls Mainboard-side interfaces such as HSTX.
- **Southbridge Management Core:** The Propeller 2 Cog responsible for managing Low-Level-Drivers and communication with them. It runs in Cog 7 after bootstrap.

## Project Status

High-level unresolved design work is tracked in [Open Topics.md](Open%20Topics.md).
