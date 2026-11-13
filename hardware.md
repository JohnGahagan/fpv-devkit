# Hardware

## Parts

| Part | Model | Runs |
|---|---|---|
| Drone | BetaFPV Air75 II Freestyle (75mm, 1S) | Betaflight, Bluejay, ExpressLRS receiver |
| Flight controller | BetaFPV Matrix 1S 5IN1 II (STM32G473, 16MB Blackbox) | Betaflight |
| Radio | RadioMaster Pocket (ELRS 2.4GHz) | EdgeTX, ExpressLRS transmitter |
| Charger | VIFLY WhoopStor V3 | |

Flight controller board revision: the factory firmware identifies as `BETAFPVG473_V2` (see [Flight controller](#flight-controller) below). The board's silkscreen has not been checked yet. The revision decides the Betaflight target: `BETAFPVG473`, `BETAFPVG473_V2` or `BETAFPVG473_V3`. See [`notes/stage-00.md`](notes/stage-00.md) and [`notes/stage-01.md`](notes/stage-01.md).

## Firmware versions

| Component | As shipped | Current | Changed in |
|---|---|---|---|
| Betaflight (FC) | 2026.6.0-alpha, commit `e92c10887`, built May 15 2026 (Cloud Build) | as shipped | |
| Bluejay (ESC) | not yet recorded | as shipped | |
| ExpressLRS receiver | not yet recorded | as shipped | |
| EdgeTX (Pocket) | not yet recorded | as shipped | |
| ExpressLRS transmitter (Pocket) | not yet recorded | as shipped | |

## Builds not yet flashed

| Firmware | Build | File |
|---|---|---|
| Betaflight | 2026.12.0 development build (not a release), commit 141dd74, target `BETAFPVG473_V2` | `firmware/betaflight_2026.12.0_STM32G474_BETAFPVG473_V2.hex` (on the share, not in this repo) |

## Flight controller

All values come from the factory CLI captures in [`configs/betaflight/`](configs/betaflight/) (2026-11-13), unless a row says otherwise.

### Board and chips

| Item | Value | Source |
|---|---|---|
| Target | `BETAFPVG473_V2`, manufacturer ID `BEFH` | `version` / `diff` |
| MCU | STM32G474xx, 168 MHz (firmware banner: `STM32G47X (G473)`) | `status` / `version` |
| Gyro / accelerometer | BMI270 on SPI1 (`GYRO: (1) BMI270 enabled locked dma`, `ACC: BMI270`) | `status` / `dump` |
| OSD chip | MAX7456 (30 x 13), SPI2, NTSC | `status` / `dump` |
| Blackbox flash | JEDEC ID `0x00852018`, reported as `16M`, SPI3, `blackbox_device = SPIFLASH` | `status` / `dump` |

The board config is `src/config/configs/BEFH/BETAFPVG473_V2/config.h` in the Betaflight tree. It defines `USE_ACCGYRO_BMI270`, along with ICM42622P, ICM42688P and LSM6DSK320X gyro drivers.

### Stock firmware

| Item | Value |
|---|---|
| Version | Betaflight 2026.6.0-alpha |
| Commit | `e92c10887` |
| Build date | May 15 2026, 06:14:55 |
| MSP API | 1.48 |
| Config rev | `6703db5` |
| Build key | `3fcfddcd33eae651632dee98e6eeb2f5` |
| Origin | Betaflight Cloud Build from upstream `master`. Commit `e92c10887` is public and is an ancestor of my own build at `141dd74`. |

### UART map

| Port | Pins (TX / RX) | Function | Serial setting |
|---|---|---|---|
| VCP (USB) | | MSP (Configurator) | `serial VCP 1 ...` |
| UART1 | A09 / A10 | **free** | `serial UART1 0 ...` |
| UART2 | A02 / A03 | VTX SmartAudio | `serial UART2 2048 ...` |
| UART3 | B10 / B11 | Serial RX, CRSF (`serialrx_provider = CRSF`) | `serial UART3 64 ...` |
| UART4 | C10 / C11 | **free** | `serial UART4 0 ...` |
| UART5 | not assigned (`NONE`) | | |

These are the `config.h` defaults (`VTX_SMARTAUDIO_UART SERIAL_PORT_USART2`, `SERIALRX_UART SERIAL_PORT_USART3`), so they appear in the dump but not in the diff.

### Motors

| Item | Value |
|---|---|
| Protocol | `motor_pwm_protocol = DSHOT300` |
| Bidirectional DShot | `dshot_bidir = ON` (RPM filter: 3 harmonics) |
| DShot bitbang | `dshot_bitbang = AUTO` |
| Motor poles | `motor_poles = 12` |
| Motor KV | `motor_kv = 1960` |
| Motor idle | `motor_idle = 550` (5.50%) |
| Direction | `yaw_motors_reversed = ON` (props out) |
| Mixer | QUADX, motors on B00, B01, C06, A04 |

### AUX switches

| Channel | Range (µs) | Function | CLI line |
|---|---|---|---|
| AUX1 (CH5) | 1700–2100 | ARM | `aux 0 0 0 1700 2100 0 0` |
| AUX2 (CH6) | 900–1300 | ANGLE | `aux 1 1 1 900 1300 0 0` |
| AUX2 (CH6) | 1300–1700 | HORIZON | `aux 2 2 1 1300 1700 0 0` |
| AUX2 (CH6) | 1700–2100 | no mode set (acro) | |
| AUX3 (CH7) | 1700–2100 | FLIP OVER AFTER CRASH (turtle) | `aux 4 35 2 1700 2100 0 0` |
| AUX4 (CH8) | 1700–2100 | BEEPER | `aux 3 13 3 1700 2100 0 0` |

Mode numbers are Betaflight permanent IDs (0 ARM, 1 ANGLE, 2 HORIZON, 13 BEEPER, 35 FLIP OVER AFTER CRASH). The third field is the AUX index, where 0 is AUX1.

### Failsafe

| Item | Value |
|---|---|
| Procedure | `failsafe_procedure = DROP` |
| Stage 1 delay | `failsafe_delay = 15` (1.5 s) |
| Switch mode | `failsafe_switch_mode = STAGE1` |
| Throttle | `failsafe_throttle = 1000` |
| Stage 1 channel values | `rxfail` 0–3 `a` (auto), 4–17 `h` (hold) |

None of the failsafe settings appear in the diff, so all are firmware defaults.

### VTX

| Item | Value |
|---|---|
| Band / channel | `vtx_band = 5` (RACEBAND), `vtx_channel = 3`: R3 |
| Frequency | `vtx_freq = 5732` MHz |
| Power | `vtx_power = 1` (vtxtable label `25`; labels: 25, 100, 200, 400, PIT) |
| Control | SmartAudio on UART2 |
| Low power when disarmed | `vtx_low_power_disarm = OFF` |

### Battery voltage thresholds (battery_profile 0)

| Item | Value |
|---|---|
| Max cell voltage | `vbat_max_cell_voltage = 440` (4.40 V), set in the diff |
| Full cell voltage | `vbat_full_cell_voltage = 410` (4.10 V) |
| Warning cell voltage | `vbat_warning_cell_voltage = 345` (3.45 V), set in the diff |
| Min cell voltage | `vbat_min_cell_voltage = 330` (3.30 V) |
| Cell detect | `vbat_detect_cell_voltage = 300` (3.00 V) |
| Voltage scale | `vbat_scale = 113`, set in the diff |
| Auto PID profile by cell count | `auto_profile_cell_count = 1` (profile 0, "GF 1614") |

### Board alignment

`config.h` sets `GYRO_1_ALIGN CW180_DEG`, which matches how the gyro chip is mounted on the board.
The stock `set align_board_yaw = -135` is applied on top of that (roll and pitch stay 0) and matches how the board sits in the frame.
`align_board_yaw` lives only in `factory_diff.txt`, because `config.h` doesn't set it. After any flash, paste the diff, or the 3D model and stick directions will be wrong.
