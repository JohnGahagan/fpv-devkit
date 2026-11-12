# fpv-devkit

A learning log for the open-source FPV flight stack: **Betaflight**, **ExpressLRS** and **EdgeTX** on real hardware, plus **ArduPilot** and **PX4** in simulation.

I'm using a small whoop-class drone as a development kit: configuring it, flashing it, reading the firmware source, and changing it. This repo holds my settings, flight logs, scripts and notes, in the order I worked through them.

## Hardware

| Part | Model | Runs |
|---|---|---|
| Drone | BetaFPV Air75 II Freestyle (75mm, 1S) | Betaflight, Bluejay, ExpressLRS receiver |
| Flight controller | BetaFPV Matrix 1S 5IN1 II (STM32G473, 16MB Blackbox) | Betaflight |
| Radio | RadioMaster Pocket (ELRS 2.4GHz) | EdgeTX, ExpressLRS transmitter |
| Charger | VIFLY WhoopStor V3 | |

Firmware versions as shipped, and after each change, are recorded in [`hardware.md`](hardware.md).

## Progress

- [ ] **0. Tools and first build:** build Betaflight from source; fly a simulated ArduPilot mission
- [ ] **1. Tour the flight controller:** Configurator tabs, UARTs, CLI
- [ ] **2. Flash:** release build, then my own build with a visible change
- [ ] **3. Motors and ESCs:** DShot, Bluejay, RPM filtering
- [ ] **4. Radio and link:** EdgeTX models, ExpressLRS binding and settings, CRSF, failsafe
- [ ] **5. Fly and log:** Blackbox logs, PID and filter tuning
- [ ] **6. Read the code:** Betaflight main loop; a Python script that talks MSP to the drone
- [ ] **7. Embedded C:** STM32 dev board, my own filter and PID loop
- [ ] **8. ExpressLRS internals:** build from source, Lua telemetry script
- [ ] **9. Autopilots:** ArduPilot and PX4 in simulation, MAVLink, scripted missions

Each stage has a write-up in [`notes/`](notes/): what I did, what broke, and what I learned.

## Repo layout

```
configs/betaflight/   CLI settings: factory diff, then one file per change
configs/edgetx/       radio model and settings files
logs/                 selected Blackbox logs, each with a note on what it shows
scripts/              my own code
notes/                one file per learning stage
hardware.md           parts and firmware versions
```

## Firmware changes

Changes to upstream projects live on branches in my forks, linked here as I make them.

| Project | Branch | What it changes |
|---|---|---|
| | | |

## Upstream projects

- [Betaflight](https://github.com/betaflight/betaflight): flight firmware
- [ExpressLRS](https://github.com/ExpressLRS/ExpressLRS): radio link
- [EdgeTX](https://github.com/EdgeTX/edgetx): radio handset firmware
- [Bluejay](https://github.com/bird-sanctuary/bluejay): motor controller firmware
- [ArduPilot](https://github.com/ArduPilot/ardupilot) and [PX4](https://github.com/PX4/PX4-Autopilot): autopilots
- [MAVLink](https://github.com/mavlink/mavlink): autopilot messaging protocol

## License

My own scripts and notes are MIT licensed (see [`LICENSE`](LICENSE)). Firmware changes in my forks keep each upstream project's license.
