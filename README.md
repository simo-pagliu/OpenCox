# OpenCox

Open source rowing telemetry: an on-boat logger built on a Raspberry Pi Pico
2 W, and a web app that turns its logs into distance, speed, pace and
stroke-rate analysis.

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

## History

Both submodules were split out of
[simo-pagliu/Open-Cox](https://github.com/simo-pagliu/Open-Cox), which remains
as an archive of the combined history up to the split.

## License

MIT
