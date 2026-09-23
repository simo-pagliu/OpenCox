# OpenCox

Open source rowing telemetry: an on-boat logger built on a Raspberry Pi Pico
2 W, and a web app that turns its logs into distance, speed, pace and
stroke-rate analysis.

**Status: Alpha.** The logger and the analysis web app both work end
to end. See the [Roadmap](#roadmap) for what that does and doesn't cover yet.

## What's in this repository

This repository is an umbrella. The code lives in two submodules:

| Submodule | Contents |
|-----------|----------|
| [OpenCox-RP2350](https://github.com/simo-pagliu/OpenCox-RP2350) | MicroPython firmware for the Pico 2 W. Records accelerometer, GPS and stroke data to microSD as CSV. |
| [OpenCox-WebUI](https://github.com/simo-pagliu/OpenCox-WebUI) | Flask web app and analysis pipeline. Reads those CSVs and produces the analysis. |

The two are joined only by the CSV log format, which the firmware writes and
the analysis pipeline reads. They are versioned separately so the boat
hardware and the web app can move at their own pace.

## Roadmap

OpenCox advances in stages. A stage is only complete once its features have
been verified on real hardware, not merely implemented.

| Stage | Goal | State |
|-------|------|-------|
| Foundations | Collect, store, analyze | Done (23/09/2026) |
| Alpha | Live display and minimal on-boat controls | **Current** |
| Beta | Richer display, stroke shape, wireless transfer | Next |
| 1.0 | Soft power-off, live telemetry to the coach | Planned |
| 2.0 | Custom PCB | Idea |

### Foundations (done)

The code was kept private while the basics were worked out.

- **On-boat logging works.** The Pico 2 W firmware records accelerometer,
  gyroscope, GPS and stroke data to microSD as CSV, unattended, for a full
  outing.
- **Offline analysis works.** The web app (either the standalone, no-install
  build or the Flask app) loads a recorded CSV and produces distance, speed,
  pace and stroke-rate splits, an interactive map, and profile charts, with a
  draggable range selector for analyzing any part of the outing in isolation.

### Alpha (current)

- Test the live stroke-rate (SPM) readout on the display.
- Add buttons for minimal control: pause/resume, reset distance, and maybe a
  "flag" feature.
- Auto-pause when rowing stops.

**Blocking:** a working display is needed before moving to Beta. The firmware
drives a 20x4 character LCD, it has been tested on real hardware... until I broke it in August 2026. It was also very difficult to read under the sun so I'm investigating better solutions.

### Beta (next)

Ships once the on-boat display is verified against real hardware.

- Improve the information shown on the display.
- Identify the duration of the catch and exit blade movements and extract the
  "shape" of the drive.
- (tentative) Wireless log transfer over the Pico 2 W's onboard Wi-Fi, replacing pulling
  the microSD card by hand after every outing.

### 1.0 (planned)

- **Proper power-off.** Right now "off" means physically cutting power, which
  drops the GPS's almanac and ephemeris state and costs several minutes to
  reacquire a fix on the next power-up. A soft power-off that avoids this is
  planned; the exact approach is still to be worked out.
- **(tentative) Live telemetry.** Stream to a coach's phone or tablet, e.g.
  from a following motorboat, over Wi-Fi or Bluetooth, so stroke rate and pace
  can be watched live instead of only reviewed after the outing.

### 2.0 (idea)

- A custom PCB integrating the Pico 2 W, MPU6050, GPS module, microSD and LCD
  wiring, replacing the current point-to-point wired build.

## License

MIT

## Cloning and deployment

Clone the umbrella together with both submodules:

```bash
git clone --recurse-submodules https://github.com/simo-pagliu/OpenCox.git
```

If you already cloned without `--recurse-submodules`:

```bash
git submodule update --init --recursive
```

### Working with the submodules

Each submodule is a normal git repository. Commit and push inside it as usual,
then record the new pointer here:

```bash
cd OpenCox-WebUI
git add -A && git commit -m "..." && git push

cd ..
git add OpenCox-WebUI
git commit -m "Bump OpenCox-WebUI"
git push
```

To pull everyone else's submodule updates:

```bash
git submodule update --remote --merge
```
