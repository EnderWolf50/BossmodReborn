# BossModReborn Taiwan API 13 maintenance record

## Lineage and baseline

- Authoritative upstream: <https://github.com/FFXIV-CombatReborn/BossmodReborn>
- Regional branch: `api13`
- Selected upstream tag: `7.3.5.0`
- Selected upstream commit: `5f4602bc660770d206b8043f3e3101b2879f047b`
- Pinned FFXIVClientStructs commit: `e279b05e9a6d3a0eff85e7bcaec0fe523358afba`
- Target environment: preserved Taiwan Dalamud API 13, .NET 9, resolved through `$DALAMUD_HOME`

## Why this baseline is pinned

The later upstream API 13 tag `7.3.8.4` loads a mandatory `InventoryAck` hook from `WorldStateGameSync`. On the Taiwan client, Dalamud reported that its hard-coded machine-code signature could not be found, which aborted `WorldStateGameSync` and then the plugin constructor.

A confirmed-working Taiwan fork, `cycleapple/RotationSolverReborn` branch `api13-tw`, is based exactly on the corresponding official `7.3.5.0` game-version line. Its compatibility strategy is historical version and dependency pinning; it does not add Taiwan-specific signatures. BossModReborn's `7.3.5.0` tag predates the later mandatory `InventoryAck` hook and retains `DalamudApiLevel` 13, `net9.0-windows`, and `DalamudPackager` 13.1.0.

This branch therefore follows the known-working regional version line rather than guessing a replacement signature from a newer client.

## Behavioral difference from upstream 7.3.8.4

The 7.3.5.0 baseline does not include the later InventoryAck world-state tracking added by upstream commit `75f9a969f`. This is deliberate: the Taiwan 7.3.5-compatible client does not expose the later signature observed by that code. No signature bytes are replaced or wildcarded, and no scanner exception is suppressed.

## Verification

Mechanically verified:

- Manifest declares Dalamud API 13.
- The post-7.3.5 mandatory InventoryAck signature is absent.
- FFXIVClientStructs is initialized at the pinned submodule commit.
- Restore/build/package checks use the preserved `$DALAMUD_HOME` environment.
- Release deployment must be hash-verified.

Pending runtime checks after each build:

1. Reload BossModReborn in the Taiwan client.
2. Confirm the plugin constructor completes without a signature exception.
3. Enter a duty and verify world-state, encounter modules, movement hints, and autorotation behavior.
4. Record any subsequent failing mandatory signature separately; do not assume all signatures are region-compatible from build success alone.

## Rebase notes

When evaluating a newer API 13 upstream tag, compare its game-version line and all mandatory `HookFromSignature`/`ScanText` additions against this baseline. A manifest that still declares API 13 is insufficient evidence of Taiwan binary compatibility. Keep `main` available for upstream tracking and carry regional decisions only on `api13`.
