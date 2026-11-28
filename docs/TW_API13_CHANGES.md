# BossModReborn Taiwan API 13 maintenance

## Lineage

- Upstream: <https://github.com/FFXIV-CombatReborn/BossmodReborn> (remote `origin`, default branch `main`). The local `main` branch tracks it.
- Base: `5f4602bc660770d206b8043f3e3101b2879f047b`, tag `7.3.5.0`, committed 2025-10-07. It pins submodule `FFXIVClientStructs` at `e279b05e9a6d3a0eff85e7bcaec0fe523358afba`.
- The base is deliberately earlier than upstream's last API 13 revision. That revision is tag `7.3.8.4` (`eac2da11f567fa3163747090ff4ff24dbd2d350b`, 2025-12-16). `git log 5f4602b..origin/main -G'DalamudApiLevel' -- manifest.json` first finds `067b063ed` "Update manifest.json" (2025-12-18, tag `7.4.0.0`), which sets `DalamudApiLevel` to 14.
- Why the base is earlier: `7.3.8.4` loads a mandatory `InventoryAck` hook from `WorldStateGameSync`. On the Taiwan client, Dalamud could not find its hard-coded signature, which aborted `WorldStateGameSync` and then the plugin constructor. `7.3.5.0` predates the hook and still has `DalamudApiLevel` 13, `net9.0-windows`, and `DalamudPackager` 13.1.0.
- Compared against: `cycleapple/RotationSolverReborn` branch `api13-tw`, a Taiwan fork confirmed working in game. It is based on the official `7.3.5.0` game-version line and pins historical versions and dependencies instead of adding Taiwan-specific signatures. This branch follows the same version line.

## Changes

None. The branch is the upstream base unchanged. Local commit `02852306f` adds only `AGENTS.md` and this record.

The base lacks the `InventoryAck` world-state tracking that upstream `75f9a969f` added later. No signature bytes are replaced or wildcarded, and no scanner exception is suppressed.

## Build and package

Target: `net9.0-windows`, `DalamudPackager` 13.1.0, .NET 9, assemblies from `$DALAMUD_HOME`.

Build command, run from the repository root:

```bash
dotnet restore BossMod/BossModReborn.csproj --locked-mode "-p:DalamudLibPath=$DALAMUD_HOME/" --nologo
dotnet build BossMod/BossModReborn.csproj -c Release --no-restore "-p:DalamudLibPath=$DALAMUD_HOME/" --nologo -p:Version=7.3.5.0 -p:AssemblyVersion=7.3.5.0 -p:FileVersion=7.3.5.0
```

Last verified build: 2026-11-29, commit `a60438b46`, through `scripts/build_plugins.py` in the home repo:

- `DalamudApiLevel`: 13.
- `AssemblyVersion`: 7.3.5.0 in the DLL, upstream's release tag `7.3.5.0`, which points at the base; upstream's CI passes the tag as `AssemblyVersion`. The published manifest says 7.3.5.1 (Taiwan patch 1; see the home repo's `AGENTS.md`, Registry).
- Package: `.local/packages/BossModReborn/latest.zip` in the home repo, the build's `BossMod/bin/Release/BossModReborn/latest.zip` with that manifest version.
- `latest.zip` SHA-256: `b202c5858a646ac01fe07ca690e123ce2e2deb09d901c0f576e4e5ff0339f502`.

This build supersedes the earlier package record (`AssemblyVersion` 7.3.5.0, built 2026-08-06 by package timestamp).

Earlier mechanical checks, not repeated for this build:

- The mandatory `InventoryAck` signature added after 7.3.5 is absent.
- `FFXIVClientStructs` is checked out at the pinned commit.
- Restore, build, and package use `$DALAMUD_HOME`.
- A Release deploy must be hash-verified.

## Runtime verification

Confirmed in game: `7.3.8.4` failed in the plugin constructor on the missing `InventoryAck` signature (date not recorded). The ffxiv_bossmod record describes the BossModReborn `7.3.5.0` line as previously runtime-validated on the Taiwan client. This record has no date or details for that check.

Pending after each build:

1. Reload BossModReborn in the Taiwan client.
2. Confirm the plugin constructor completes without a signature exception.
3. Enter a duty and check world state, encounter modules, movement hints, and autorotation.
4. Record each later failing mandatory signature on its own. A passing build does not show that all signatures work on the Taiwan client.

## Rebase policy

To evaluate a newer API 13 upstream tag, compare its game-version line and every mandatory `HookFromSignature` and `ScanText` addition against `7.3.5.0`. A manifest that still declares API 13 is not evidence of Taiwan binary compatibility. Keep `main` for upstream tracking, and carry regional decisions only on `api13`.
