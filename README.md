# ClimateDT Experiment Descriptions

Human readable descriptions and environment-specific configuration for ClimateDT experiments. Used by the ClimateDT Evaluation Charts (dashboard).

## File structure

| File | Purpose |
|---|---|
| `common.yaml` | Shared config for all environments (descriptions, labels, ordering, etc.) |
| `dev.yaml` | Overrides for the **dev** internal dashboard |
| `internal.yaml` | Overrides for the **internal** production dashboard |
| `desp_staging.yaml` | Overrides for the **DESP staging** dashboard |
| `desp.yaml` | Overrides for the **DESP production** dashboard |

All dashboards read from the **main** branch. The signer service fetches `common.yaml` + the environment-specific file (e.g. `desp.yaml`), deep-merges them (environment wins on conflicts), and serves the result.

### Environment mapping

| Environment file | Dashboard | URL |
|---|---|---|
| `dev.yaml` | Internal dev | [Dev dashboard](https://dev-climatedt-internal-dashboard.2.rahtiapp.fi/) |
| `internal.yaml` | Internal production | [Internal dashboard](https://climatedt-internal-dashboard.2.rahtiapp.fi/) |
| `desp_staging.yaml` | DESP staging | [DESP staging dashboard](https://climatedt-desp-staging-dashboard.2.rahtiapp.fi/) |
| `desp.yaml` | DESP production | [DESP production dashboard](https://climatedt-desp-dashboard.2.rahtiapp.fi/) |

## Workflow

1. **Descriptions** (shared across all environments): edit `common.yaml` on **main**.
2. **Environment-specific overrides** (hiding, default toggles, description overrides for one env): edit the corresponding env file (e.g. `desp.yaml`).
3. Push to **main**. Changes appear within ~30 seconds (signer cache TTL).

Keys in an environment file override matching keys in `common.yaml` via deep merge. For example, a description in `desp.yaml` overrides the same experiment's description from `common.yaml` on the DESP dashboard, while other dashboards still see the `common.yaml` version.

## Supported keys

See `DESCRIPTIONS_CONFIG.md` in the dashboard repository for the full reference of supported keys (`experiment_description`, `hide`, `default_enabled`, `diagnostics`, `projects`, `models`, etc.).
