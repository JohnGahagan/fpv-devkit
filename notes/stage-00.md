# Stage 00: Dev environment setup

Date: 2026-11-12

## Layout

| What | Where | Why there |
|---|---|---|
| This repo (notes, configs, scripts) | `~/fpv-share/fpv-devkit` (VirtualBox share `fpv-share`) | Visible from Windows |
| Built firmware for flashing | `~/fpv-share/firmware/` | Flashing happens on the Windows host |
| Betaflight source + build | `~/src/betaflight` | Needs a real Linux filesystem |
| ArduPilot source + build | `~/src/ardupilot` | Same |
| Repo's Python venv | `~/.venvs/fpv-devkit` | The share can't hold symlinks, and venvs need them |
| ArduPilot's Python venv | `~/venv-ardupilot` (made by the prereqs script) | |

Limits of the shared folder, tested: no symlinks (`Permission denied`), case-insensitive filenames, and every file shows as mode 777.
Because of the last two, this repo has `git config core.fileMode false` and `core.ignorecase true` set (local repo config only).

## Installed versions

| Component | Version |
|---|---|
| build-essential / gcc | 12.12ubuntu2.26.04.2 / gcc 15.2.0 |
| git | 2.53.0 |
| curl / wget | 8.18.0 / 1.25.0 |
| Python | 3.14.4 (python3-pip 25.1.1, python3-venv 3.14.3) |
| gh | 2.46.0 |
| GNU Make | 4.4.1 |
| Arm toolchain (Betaflight, `make arm_sdk_install`) | Arm GNU Toolchain 13.3.Rel1 (gcc 13.3.1) |
| Betaflight | commit 141dd74 (2026-11-12), firmware version 2026.12.0 |
| ArduPilot | commit eec26ae (2026-11-12) |
| MAVProxy / pymavlink / wxPython (ArduPilot venv) | 1.8.75 / 2.4.50 / 4.3.2 |
| pymavlink / pyserial (repo venv) | 2.4.50 / 3.5 |

System changes: ModemManager removed (it grabs serial ports). User added to `dialout` and `vboxsf`, which takes effect after the next login.
Also installed: `virtualbox-guest-utils`. It was outside the package list; it was used to find the share's real name with `sudo VBoxControl sharedfolder list`.

## Betaflight target choice

BetaFPV has three G473 configs: `BETAFPVG473`, `BETAFPVG473_V2` and `BETAFPVG473_V3`. All use an ICM42688P gyro, SPI blackbox flash, MAX7456 OSD and CRSF RX on UART3.
They differ only in board-revision pin details: flash CS is PB9 on V1 and PC13 on V2 and V3; V3 adds gyro CLKIN and a VTX power switch.
**Built: `BETAFPVG473_V2`.** The physical board revision is not yet known, so **check the board's silkscreen or BetaFPV's product page before flashing.** The wrong variant boots but has dead peripherals.

Output: `obj/betaflight_2026.12.0_STM32G474_BETAFPVG473_V2.hex` (1,108,022 bytes), copied to `~/fpv-share/firmware/`.

## What failed and how it was fixed

1. **Shared folder not mounted.** The share is named `fpv-share`, not `fpv-drone`.
   Fix: `sudo mount -t vboxsf -o uid=$(id -u),gid=$(id -g) fpv-share ~/fpv-share`. After you log out and back in (vboxsf group), it also auto-mounts at `/media/sf_fpv-share`.
2. **ArduPilot prereqs, first run:** the log stopped partway through "Building wheel for wxpython". The run was interrupted, most likely by a command timeout. There was no error.
3. **ArduPilot prereqs, second run:** `ERROR: Failed building wheel for wxpython` with `[Errno 122] Disk quota exceeded`.
   Cause: pip builds in `/tmp`, which here is a 3.7 GB tmpfs with per-user quotas (`usrquota`). The root disk had 95 GB free.
   Fix: `TMPDIR=$HOME/.cache/pip-build-tmp Tools/environment_install/install-prereqs-ubuntu.sh -y`, which finished with exit 0.
4. Side effect: each prereqs run appended `source ~/venv-ardupilot/bin/activate` to `~/.profile`, so it now appears 3 times. This is harmless and can be trimmed by hand.
   The script also added `/opt/gcc-arm-none-eabi-10-2020-q4-major/bin`, `Tools/autotest` and ccache to PATH.

## Commands that mattered

```bash
# Betaflight
cd ~/src/betaflight
make arm_sdk_install
make configs
make CONFIG=BETAFPVG473_V2

# ArduPilot
cd ~/src/ardupilot
git submodule update --init --recursive
TMPDIR=$HOME/.cache/pip-build-tmp Tools/environment_install/install-prereqs-ubuntu.sh -y
./waf configure --board sitl
./waf copter
```

## Rebuild Betaflight yourself

```bash
cd ~/src/betaflight
make CONFIG=BETAFPVG473_V2
ls -la obj/*.hex
cp obj/betaflight_*_BETAFPVG473_V2.hex ~/fpv-share/firmware/
```

## Launch SITL yourself

```bash
source ~/venv-ardupilot/bin/activate     # already in ~/.profile for login shells
cd ~/src/ardupilot
./waf configure --board sitl && ./waf copter   # only after source changes
Tools/autotest/sim_vehicle.py -v ArduCopter --no-rebuild
```

Ready state: MAVProxy prints `AP: ArduPilot Ready`, then `AP: EKF3 IMU0 is using GPS`. Type `exit` (or Ctrl-C) to stop.
Add `--map --console` for the GUI windows.

## Repo venv

```bash
source ~/.venvs/fpv-devkit/bin/activate
```
