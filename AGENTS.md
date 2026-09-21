# Repository Guidance

- Assume nothing in this repository is implemented yet. The repository describes the design of the first version.
- Never frame design changes in terms of legacy compatibility or preserving behavior of previously deployed versions.

## Terminology

- A **Card** is either a Mainboard or an Expansion Card.
- A **Slot** is either the single Mainboard Slot or one of seven Expansion Slots.
- Do not use `Expansion Card Slot`; use `Expansion Slot`.
- Do not use `Mainboard Card`; use `Mainboard`.
- Do not use `Expansion` as a standalone noun.
- Do not use `Port` / `port` for Card/Slot receptacles; use `Slot`.
- Do not use `Expansion Port`; use `Expansion Slot`.
- Do not use `Card Port`; use `Slot` or the specific Slot type.
- Do not use `Mainboard Port`; use `Mainboard Slot`.
- A Propeller 2 CPU is called a **Cog**. Do not call a Propeller 2 Cog a **Core**.
