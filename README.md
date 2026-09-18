# FFXV COMRADES Save Editor v1.8.3

Modern Windows-oriented save editor for **FINAL FANTASY XV: COMRADES**.

## Main editors

- Dashboard / save health
- Gil + kW
- Character / Glaive persistent `avatar0` stats
- Chocobo Stats
- Player Weapons
- Abilities / Sigils
- Collections
- Inventory
- Database

Equipment is intentionally not exposed as a separate editor page.


## Chocobo Stats

The Chocobo page edits **existing registered COMRADES chocobos** in `gameplay0`. The schema mapping comes directly from `Black.Save.Multiplay.SaveChocoboDataStruct.ChocoboDataStruct`.

Editable fields:

- Current Level
- Level Cap
- Limit Max Level
- Skill Level
- Stamina
- Jump
- Top Speed (float / mph display value)
- Injury Percent

The editor preserves chocobo identity, personality, rarity, color, training arrays, injury flags, and all unknown bytes. It does **not** create a new chocobo in an empty slot or modify registration/unlock flags.


**Runtime note:** the previously supplied gameplay0 samples contain `0` registered chocobos, so the new writer has been validated structurally and with a synthetic serialized record, but still needs one live in-game round-trip using a save that actually contains your Black Chocobo.

## Player Weapons

The player-weapon editor targets the active Glaive's real owned/equipped weapon instances. It supports the mapped display fields used by the current research set, including current level, level cap, attack, visible bonus stats, signed resistances, and verified weapon abilities.

Verified real-name ability mappings retained in this release:

- Penetrator X = `0x0105E7CA`
- Punisher II = `0x0105CE8F`
- None = `0x01009E27`

COMRADES serializes the two weapon ability links in reverse display order. The editor compensates for this so **Ability 1** is the left in-game ability and **Ability 2** is the right in-game ability.

Unknown/unmapped ability IDs are preserved rather than renamed or replaced automatically.

## v1.8.3 Chocobo editor

This release adds a schema-backed Chocobo Stats page for registered chocobos while retaining all v1.7.0 deep-review reliability fixes.

## v1.7.0 reliability improvements

This release adds source-safe export handling, failed-Apply cleanup, duplicate-weapon instance handling, signed resistance preservation, backend protocol/version verification, process timeouts, improved input validation, dirty-state indication, keyboard shortcuts, and a more responsive UI.

Existing files in an export destination are backed up before replacement. The editor will not intentionally overwrite the original loaded save directory as the modded output destination.

## Run from source

Double-click `RUN-EDITOR.cmd` or run:

```text
pythonw FFXV_COMRADES_Save_Editor.pyw
```

Python 3.11+ with Tkinter is required.

## Build a Windows EXE

Run `BUILD-WINDOWS-EXE.cmd`.

The build script packages:

- `Bundled\ForgeCore.exe`
- `assets\app_icon.ico`
- all UI assets under `assets\`

The normal build output is:

`dist\FFXV_COMRADES_Save_Editor_v1.8.3.exe`

## Safety

Always keep your own backup before using a modified game save.

The editor deliberately preserves unknown and unexposed fields wherever possible. Structural validation cannot prove that every extreme stat combination is accepted by every game version.

`MAX ALL` on weapons remains an advanced/aggressive preset. Very large attack values can display in the game while producing incorrect combat damage, including 0-1 actual damage. Use manual moderate values when gameplay reliability matters.

## Evidence notes

- The weapon structure does not expose a verified standalone EXP field.
- Current weapon level maps to the field historically named `RemodelFailCount` in the extracted schema.
- Weapon level cap maps to `status_adjust[17]`.
- Resistance display is the signed inverse of the raw save scalar: `display = -raw`.
- Only the localized ability IDs listed above are currently exposed by name.
- Unknown current ability IDs remain preserved.
- Registered chocobo stats are stored in fixed-size `ChocoboDataStruct` records inside the `ChocoboDataList`; v1.8.3 edits only the validated scalar members.
