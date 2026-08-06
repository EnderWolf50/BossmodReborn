# BossModReborn API 13 regional maintenance

This checkout keeps `main` aligned with the authoritative `FFXIV-CombatReborn/BossmodReborn` upstream and uses the concise `api13` branch for the preserved Taiwan Dalamud API 13 environment.

Before changing the API 13 branch, read `docs/TW_API13_CHANGES.md`.

## Invariants

- Build against `$DALAMUD_HOME`; do not substitute a current global Dalamud installation.
- Keep the API 13 baseline aligned with the Taiwan game-version line, not merely the newest upstream tag whose manifest still says API 13.
- Do not copy signatures from newer game/API versions without evidence from the target Taiwan executable.
- Treat successful build/package verification separately from in-game plugin-load verification.
- Record any future Taiwan-specific signature, client-structure, or localized behavior change in the maintenance note.
