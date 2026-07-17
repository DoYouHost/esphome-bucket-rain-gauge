# Bucket Rain Gauge

ESPHome firmware for an **ESP32-C3** driving a tipping-bucket rain gauge.
Rainfall is reported to Home Assistant over the native ESPHome API, with OTA
updates delivered through the DoYouHost `beta` and `production` channels.

The CI pipeline (build → pre-release → R2 → beta → production promotion) is
shared via [`DoYouHost/workflows`](https://github.com/DoYouHost/workflows)
and [`DoYouHost/ESPHomeTemplates`](https://github.com/DoYouHost/ESPHomeTemplates).

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
