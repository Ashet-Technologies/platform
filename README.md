# platform

## Glossary

- **Backplane:** The interconnect between the Mainboard and Expansion Cards.
- **Southbridge:** The Propeller 2 on the Backplane dispatching data between the Mainboard and Expansion Cards. Provides the main electrical expansion interface.
- **Mainboard:** Contains the CPU and RAM of the system and runs the OS. Connects to the Backplane.
- **Expansion Card:** A generic expansion that provides additional, user-selectable features to the computer.
- **Not-so-mainboard:** A Mainboard that supports being plugged into an Expansion Card slot and can be used as a coprocessor of sorts. It accepts commands or tasks when used in an Expansion Card slot.
- **Low-Level-Driver:** The piece of firmware running on the Propeller 2 Cog associated with its Expansion Card Slot.
- **Southbridge Management Core:** The Propeller 2 Cog responsible for managing Low-Level-Drivers and communication with them. It runs in Cog 7 after bootstrap.

## Project Status

High-level unresolved design work is tracked in [Open Topics.md](Open%20Topics.md).
