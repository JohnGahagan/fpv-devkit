# Stage 01: Tour the flight controller

Date: 2026-11-13

Status: in progress.

## What I did

The drone arrived. Before changing anything, I connected it to the development Configurator (`master.app.betaflight.com`) and captured its factory configuration from the CLI:

| Command | Saved as |
|---|---|
| `version` | `configs/betaflight/version_status.txt` |
| `status` | `configs/betaflight/status.txt` |
| `diff all` | `configs/betaflight/factory_diff.txt` |
| `dump all` | `configs/betaflight/factory_dump.txt` |

The `mcu_id` values are redacted. Every other line is as captured. See [`configs/betaflight/README.md`](../configs/betaflight/README.md).
The values I read from these files are in the flight controller section of [`hardware.md`](../hardware.md#flight-controller).

What the board reports:

| What | Value |
|---|---|
| Target | `BETAFPVG473_V2` (manufacturer `BEFH`) |
| Firmware | Betaflight 2026.6.0-alpha, commit `e92c10887`, built May 15 2026 (Cloud Build from upstream master) |
| MCU | STM32G474xx, 168 MHz |
| Gyro / acc | BMI270 |
| OSD / flash | MAX7456 / JEDEC `0x00852018` (16M) |

## What failed and how it was fixed

1. **The Configurator offered only "Connect (Virtual)".**
   Fix: select the drone's real serial device in the port list. The Connect button then connects to the board.

## Finding: gyro and firmware choice

BetaFPV's card warns against flashing official release firmware, because some units have gyros that the 4.5.x releases don't support.
I checked this unit:

- `status` reports `GYRO: (1) BMI270` and `ACC: BMI270`.
- In current upstream master, `src/config/configs/BEFH/BETAFPVG473_V2/config.h` defines `USE_ACCGYRO_BMI270`.
- The stock firmware's commit `e92c10887` is an ancestor of my build at `141dd74`.

So my own `BETAFPVG473_V2` build (2026.12.0 development build, not a release) should run on this unit. **Not flashed yet.**

## Flash plan (for later)

1. Download BetaFPV's fallback firmware first, so I can go back if my build doesn't work.
2. Take the props off.
3. Flash my `BETAFPVG473_V2` build.
4. Run `status` and check that it reports `GYRO=BMI270`.
5. Paste `configs/betaflight/factory_diff.txt` into the CLI. It restores `align_board_yaw = -135`, the AUX modes, VTX table and tuning.
6. On the Setup tab, confirm the 3D model follows the drone when I tilt and turn it.

## Still to do

- Configurator screenshots.

## UART map in my own words

