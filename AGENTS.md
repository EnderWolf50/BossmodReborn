# BossModReborn Taiwan API 13 branch

`api13` keeps FFXIV-CombatReborn/BossmodReborn at an earlier Dalamud API 13 revision than its last one, for the Taiwan client (game 7.3), plus the changes in `docs/TW_API13_CHANGES.md`. Read that record before changing this branch.

## Invariants

- Build against `$DALAMUD_HOME`. Do not substitute a current global Dalamud installation.
- Keep the base on the Taiwan game-version line, not on the newest upstream tag whose manifest still says API 13.
- Do not copy signatures from newer game or API versions without evidence from the Taiwan executable.
- Treat a passing build and package check separately from an in-game plugin load.
- Record every Taiwan-specific signature, client-structure, or localized behavior change in `docs/TW_API13_CHANGES.md`.
