# platform

## Glossary

- **Card:** A module that plugs into a Slot. A Card is either a Mainboard or an Expansion Card.
- **Slot:** A receptacle for a Card. A Slot is either the Mainboard Slot or an Expansion Slot.
- **Mainboard Slot:** The single Slot in a system intended for the Mainboard. There is exactly one Mainboard Slot per system.
- **Expansion Slot:** A Slot intended for an Expansion Card. The Ashet Home Computer has seven Expansion Slots.
- **Backplane:** The interconnect containing the Mainboard Slot and Expansion Slots and connecting their signals.
- **Southbridge:** The Propeller 2 on the Backplane dispatching data between the Mainboard and Expansion Cards. Provides the main electrical expansion interface.
- **Mainboard:** A Card containing the CPU and RAM of the system and running the OS.
- **Expansion Card:** A Card that provides additional, user-selectable features to the computer.
- **Not-so-mainboard:** A Mainboard that supports being plugged into an Expansion Slot and can be used as a coprocessor of sorts. It accepts commands or tasks when used in an Expansion Slot.
- **Low-Level-Driver:** Firmware running on the Propeller 2 Cog associated with an Expansion Slot. It translates packet/datagram traffic into the card-specific electrical protocol and does not directly provide OS functionality.
- **Expansion Card Driver:** Driver code running on the Mainboard that exposes an Expansion Card's functionality to the Mainboard operating system. It communicates with the card through the Southbridge/Low-Level-Driver path and controls Mainboard-side interfaces such as HSTX.
- **Southbridge Management Core:** The Propeller 2 Cog responsible for managing Low-Level-Drivers and communication with them. It runs in Cog 7 after bootstrap.

## Terminology

Use **Card** when a statement applies to both Mainboards and Expansion Cards.

Use **Slot** when a statement applies to both the Mainboard Slot and Expansion Slots.

Avoid these terms:

- `Expansion Card Slot`; use **Expansion Slot**
- `Mainboard Card`; use **Mainboard**
- `Expansion` as a standalone noun; use **Expansion Card**, **Expansion Slot**, or the specific interface name
- `Port` / `port` when referring to a Card/Slot receptacle; use **Slot**
- `Expansion Port`; use **Expansion Slot**
- `Card Port`; use **Slot** or the specific Slot type
- `Mainboard Port`; use **Mainboard Slot**

## Project Status

High-level unresolved design work is tracked in [Open Topics.md](Open%20Topics.md).
