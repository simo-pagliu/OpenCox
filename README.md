# OpenCox

Open source rowing telemetry: an on-boat logger built on a Raspberry Pi Pico
2 W, and a web app that turns its logs into distance, speed, pace and
stroke-rate analysis.

**Status: Alpha.** The logger and the analysis web app are both functional
end to end — see [Project status and roadmap](#project-status-and-roadmap)
below for exactly what that does and doesn't cover yet.

This repository is an umbrella. The code lives in two submodules:

| Submodule | Contents |
|-----------|----------|
| [OpenCox-RP2350](https://github.com/simo-pagliu/OpenCox-RP2350) | MicroPython firmware for the Pico 2 W. Records accelerometer, GPS and stroke data to microSD as CSV. |
| [OpenCoxUI](https://github.com/simo-pagliu/OpenCoxUI) | Flask web app and analysis pipeline. Reads those CSVs and produces the analysis. |

The two are joined only by the CSV log format, which the firmware writes and
the analysis pipeline reads. They are versioned separately so the boat
hardware and the web app can move at their own pace.

## Cloning

```bash
git clone --recurse-submodules https://github.com/simo-pagliu/OpenCox.git
```

If you already cloned without `--recurse-submodules`:

```bash
git submodule update --init --recursive
```

## Working with the submodules

Each submodule is a normal git repository. Commit and push inside it as usual,
then record the new pointer here:

```bash
cd OpenCoxUI
git add -A && git commit -m "..." && git push

cd ..
git add OpenCoxUI
git commit -m "Bump OpenCoxUI"
git push
```

To pull everyone else's submodule updates:

```bash
git submodule update --remote --merge
```

## Log format

The contract between the two halves. One CSV per session, rows tagged by
`record_type` in the first column:

| Type | Rate | Carries |
|------|------|---------|
| `A` | 100 Hz | Accelerometer, gyroscope, stroke flag |
| `G` | 1 Hz | Position, speed, SPM, distance, satellites, HDOP |
| `S` | 1 Hz | Diagnostics: raw UART byte count, NMEA sentence count, fix flag |

Changing this format means changing both submodules together.

## Project status and roadmap

OpenCox is being developed in stages, gated on what's actually been verified
on real hardware rather than on what's merely implemented. Each stage below
is a superset of the one before it.

### Alpha (current)

- **On-boat logging works.** The Pico 2 W firmware records accelerometer,
  gyroscope, GPS and stroke data to microSD as CSV, unattended, for a full
  outing.
- **Offline analysis works.** The web app (either the standalone,
  no-install build or the Flask app) loads a recorded CSV and produces
  distance, speed, pace and stroke-rate splits, an interactive map, and
  profile charts, with a draggable range selector for analyzing any part of
  the outing in isolation.
- **The on-boat LCD display is implemented but unverified.** The firmware
  drives a 20x4 character LCD with live pace/SPM/distance, but this hasn't
  been tested against real hardware yet — the maintainer's display unit is
  currently non-functional. Treat the live display as unproven until Beta.
- **Stroke-phase detection is a first pass.** The firmware already detects
  catch and finish/exit events from the accelerometer signal (see
  `stroke_detection.py` in OpenCox-RP2350) and logs their timing and the
  acceleration shape of each stroke, but this hasn't been validated against
  real strokes at the boat, and the drive itself (its consistency, symmetry,
  etc.) isn't characterized yet. Refining and validating this is the next
  piece of work.

### Beta

Ships once the on-boat display path is implemented *and* verified against
real hardware — right now it's blocked purely on the maintainer having a
working display to test with. Also planned for this stage:

- Validated stroke-phase detection (catch/drive/finish), informed by testing
  against real strokes rather than bench data.
- Wireless log transfer over the Pico 2 W's onboard Wi-Fi, replacing pulling
  the microSD card by hand after every outing.

### 1.0

- A custom PCB integrating the Pico 2 W, MPU6050, GPS module, microSD and
  LCD wiring, replacing the current point-to-point wired build.
- Physical controls (start/stop, reset/flag) and a proper power-off. Right
  now "off" means physically cutting power, which drops the GPS's almanac
  and ephemeris state and costs several minutes to reacquire a fix on the
  next power-up — a soft power-off that avoids this is planned, exact
  approach still to be worked out.

### Exploratory (not committed to any stage yet)

- Live telemetry to a coach's phone or tablet — e.g. from a following
  motorboat — over Wi-Fi or Bluetooth, so stroke rate and pace can be
  watched live instead of only reviewed after the outing.

## History

Both submodules were split out of
[simo-pagliu/Open-Cox](https://github.com/simo-pagliu/Open-Cox), which remains
as an archive of the combined history up to the split.

## License

MIT
