# DoYouHost ESPHome Project Template

Template for creating new ESPHome projects in the DoYouHost organization.
The CI pipeline (build → pre-release → R2 → beta → production promotion)
is shared via [`DoYouHost/workflows`](https://github.com/DoYouHost/workflows)
and [`DoYouHost/ESPHomeTemplates`](https://github.com/DoYouHost/ESPHomeTemplates).

## Initializing a new project

1. Create a new repository from this template (GitHub: *Use this template*).
2. Edit [`project.yaml`](project.yaml) — this is the only file containing
   project-level metadata consumed by the workflows:
   - `project_name`
   - `r2_directory`
   - `esphome_version`
   - `files` (list of ESPHome YAML entry points)
   - `r2_cleanup_days`
3. Rename the skeleton `esp32-template.yml` to match `files[0]` from
   `project.yaml`, then fill in the project-specific configuration.
4. Generate fresh `API_KEY` and `OTA_PASSWORD` values in `substitutions`.
5. Update the `dashboard_import.package_import_url` and the manifest URLs
   to point at the new repository / R2 directory.
6. Reset `version.txt` to your starting version (e.g. `2026.1.0`) and
   start a fresh `CHANGELOG.md`.
7. In `static/_config.yaml`, leave `title:` as-is — it is rewritten by the
   `publish-pages` workflow using `project_name` from `project.yaml`.

## Required secrets

Configure these secrets in the new repository (or inherit from the org):

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
- `promote-to-prod.yaml` — fires when a GitHub release is marked as
  released; promotes the matching artifacts in R2 to `production` and
  triggers a Pages rebuild.
- `publish-pages.yaml` — builds and deploys the GitHub Pages landing page
  with embedded firmware downloads.
- `r2-cleanup.yaml` — daily cleanup of stale artifacts in R2.

## How shared parameters work

`project.yaml` is the single source of truth. The composite action at
`.github/actions/load-project-config` reads it once per workflow run and
exposes the values as job outputs. Workflows reference those outputs
instead of hard-coding strings, so a new project only needs to edit
`project.yaml` to wire up the entire pipeline.
