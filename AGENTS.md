# Repository Guidance

- Assume nothing in this repository is implemented yet. The repository describes the design of the first version.
- Never frame design changes in terms of legacy compatibility or preserving behavior of previously deployed versions.

## Terminology

- A **Card** is either a Mainboard or an Expansion Card.
- A **Slot** is either the single Mainboard Slot or one of seven Expansion Slots.
- Do not use `Expansion Card Slot`; use **Expansion Slot**.
- Do not use `Mainboard Card`; use **Mainboard**.
- Do not use `Expansion` as a standalone noun.
- Do not use `Port` / `port` for Card/Slot receptacles; use **Slot**.
- Do not use `Expansion Port`; use **Expansion Slot**.
- Do not use `Card Port`; use **Slot** or the specific Slot type.
- Do not use `Mainboard Port`; use **Mainboard Slot**.
- A Propeller 2 CPU is called a **Cog**. Do not call a Propeller 2 Cog a **Core**.

## Documentation Structure

- Normative declarations and specifications belong directly under `Platform/` or `Computer/`, depending on their scope.
- Accepted design decisions and their concise rationale belong under `Platform/Decisions/` or `Computer/Decisions/`.
- Detailed design history, background stories, experiments, and other non-normative context belong under the top-level `Lore/` directory.
- `Lore/` documents may explain how a design evolved, but must not be the only place where a normative requirement or accepted decision is recorded.
- Normative documents must contain only decided platform/computer behavior and specifications.
- Do not include open questions, unresolved alternatives, TODO-style design discussion, or references to unresolved topics in normative documents.
- Track unresolved design work only in `Open Topics.md`.
- Remove completed topics from `Open Topics.md` instead of keeping status/history text about what is already defined.
