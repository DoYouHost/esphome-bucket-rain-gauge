# Bucket Rain Gauge

ESPHome firmware for a **Seeed Studio XIAO ESP32-C3** driving a tipping-bucket
rain gauge, with onboard temperature, humidity, and pressure sensing. Rainfall
and weather are reported to Home Assistant over the native ESPHome API, with OTA
updates delivered through the DoYouHost `beta` and `production` channels.

The CI pipeline (build → pre-release → R2 → beta → production promotion) is
shared via [`DoYouHost/workflows`](https://github.com/DoYouHost/workflows)
and [`DoYouHost/ESPHomeTemplates`](https://github.com/DoYouHost/ESPHomeTemplates).

## Features

- **Rain detection** — 49E linear Hall sensor read on the ADC; each bucket tip
  toggles the `Bucket Position` state.
- **Rainfall totals** — lifetime `Total Rainfall`, plus `Today` / `This Week` /
  `This Month` / `This Year` (disabled by default; enable per device in Home
  Assistant). Period totals reset on Home Assistant's local time.
- **Rain rate** — 1-minute, 10-minute average, and hourly.
- **Weather** — AHT20 (temperature, humidity) and BMP280 (pressure) over I2C,
  with software smoothing.
- **Calibration at runtime** — `Rain Per Tip`, `Tip Threshold`, and
  `Tip On Falling Signal` controls; no reflash needed.
- **Onboarding** — Wi-Fi via Improv over BLE and Serial; BLE auto-disables
  shortly after Wi-Fi connects. The API encryption key is provisioned at
  runtime, so no key is baked into distributed firmware.

## Hardware & wiring

Target board: Seeed Studio XIAO ESP32-C3. Default pin assignments (all set via
`substitutions` in [`bucket-rain-gauge.yml`](bucket-rain-gauge.yml)):

| Signal        | XIAO pad | GPIO   | Notes                              |
| ------------- | -------- | ------ | ---------------------------------- |
| Hall analog   | A1 / D1  | GPIO3  | ADC1 channel (Wi-Fi safe)          |
| I2C SDA       | D4       | GPIO6  | AHT20 `0x38`, BMP280 `0x77`        |
| I2C SCL       | D5       | GPIO7  | needs pull-ups if not on the board |

Power the 49E Hall sensor from **3V3** (not 5V) so its output stays within the
ESP32-C3's 0–3.3 V ADC input range.

## Configuration

- [`bucket-rain-gauge.yml`](bucket-rain-gauge.yml) — the ESPHome entry point.
  Targets the Seeed Studio XIAO ESP32-C3 (`seeed_xiao_esp32c3`) with the
  ESP-IDF framework; adjust the `board:` value if your hardware differs.
- [`project.yaml`](project.yaml) — the single source of truth for
  project-level metadata consumed by the workflows (`project_name`,
  `r2_directory`, `esphome_version`, `files`, `r2_cleanup_days`).

## Required secrets

Configure these in the repository (or inherit from the org):

- `DOYOUHOST_PAT` — used to publish releases and dispatch follow-up workflows
- `CF_ACCOUNT_ID`, `CF_ACCESS_KEY_ID`, `CF_SECRET_ACCESS_KEY`,
  `CF_BUCKET_NAME` — Cloudflare R2 credentials for upload / promote / cleanup

## Triggering a build

Workflows only build when explicitly requested:

- A push or PR whose first line begins with `BUILD:` / `build:`
- A manual `workflow_dispatch` run

## Workflows

- `build-and-pre-release.yaml` — builds firmware, creates a pre-release on
  GitHub, uploads artifacts to R2, promotes to the `beta` channel.
- `promote-to-prod.yaml` — fires when a GitHub release is marked as released;
  promotes the matching artifacts in R2 to `production` and triggers a Pages
  rebuild.
- `publish-pages.yaml` — builds and deploys the GitHub Pages landing page with
  embedded firmware downloads.
- `r2-cleanup.yaml` — weekly cleanup of stale artifacts in R2 (Saturdays at
  02:00 UTC).

## Credits

The tipping-bucket rain gauge hardware design is based on
[Rain Gauge by MakerWorld](https://makerworld.com/en/models/1366151-rain-gauge#profileId-1411849).
