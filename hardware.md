# Hardware

## Parts

| Part | Model | Runs |
|---|---|---|
| Drone | BetaFPV Air75 II Freestyle (75mm, 1S) | Betaflight, Bluejay, ExpressLRS receiver |
| Flight controller | BetaFPV Matrix 1S 5IN1 II (STM32G473, 16MB Blackbox) | Betaflight |
| Radio | RadioMaster Pocket (ELRS 2.4GHz) | EdgeTX, ExpressLRS transmitter |
| Charger | VIFLY WhoopStor V3 | |

Flight controller board revision: **not yet confirmed** (check silkscreen / BetaFPV product page). It decides the Betaflight target: `BETAFPVG473`, `BETAFPVG473_V2` or `BETAFPVG473_V3`. See [`notes/stage-00.md`](notes/stage-00.md).

## Firmware versions

| Component | As shipped | Current | Changed in |
|---|---|---|---|
| Betaflight (FC) | not yet recorded | as shipped | |
| Bluejay (ESC) | not yet recorded | as shipped | |
| ExpressLRS receiver | not yet recorded | as shipped | |
| EdgeTX (Pocket) | not yet recorded | as shipped | |
| ExpressLRS transmitter (Pocket) | not yet recorded | as shipped | |

## Builds not yet flashed

| Firmware | Build | File |
|---|---|---|
| Betaflight | 2026.12.0, commit 141dd74, target `BETAFPVG473_V2` | `firmware/betaflight_2026.12.0_STM32G474_BETAFPVG473_V2.hex` (on the share, not in this repo) |
