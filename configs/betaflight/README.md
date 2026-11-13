# Betaflight CLI backups

These files hold the **factory state** of my BetaFPV Air75 II flight controller, captured on **2026-11-13** before I changed anything.
I captured them from the Betaflight CLI in the development Configurator (master.app.betaflight.com).

| File | CLI command | What it is |
|---|---|---|
| `factory_diff.txt` | `diff all` | Only the settings that differ from firmware defaults. **This is the restore file.** |
| `factory_dump.txt` | `dump all` | Every setting, defaults included. Use it for reference and to look up values. |
| `status.txt` | `status` | Detected hardware (MCU, gyro, OSD, flash), build key, and the arming flags at capture time |
| `version_status.txt` | `version` | Firmware version, commit, config rev, board and manufacturer ID |

The factory firmware is Betaflight `2026.6.0-alpha` (commit `e92c10887`, built May 15 2026), target `BETAFPVG473_V2`.

## Redactions

The only edit to these files is that each `mcu_id` value is replaced with `REDACTED`, because it is the chip's unique serial number.
Every other line is exactly as captured, including the CRLF line endings.
On restore, the `mcu_id` line does nothing: the CLI command only prints the ID and ignores any argument.

## Restoring

After flashing new firmware, open the CLI, paste the whole of `factory_diff.txt`, and press Enter.
It ends with `save`, which reboots the board.
The diff starts with `defaults nosave`, so pasting it onto any configuration gives the factory state.
The diff matches the 2026.6.0-alpha settings. On newer firmware, a renamed or removed setting gives an error on that line, so read the CLI output after pasting.
