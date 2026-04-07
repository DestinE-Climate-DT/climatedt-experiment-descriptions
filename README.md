# ClimateDT Experiment Descriptions

Human-readable descriptions and environment-specific configuration for ClimateDT experiments. Used by the ClimateDT Evaluation Charts (dashboard).

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

All keys are optional. You can use any combination in `common.yaml` or an env file.

- `projects`
- `models`
- `experiments`
- `ensembles`
- `diagnostics`
- `sub_diagnostics`
- `info_keys`
- `experiment_description`
- `default_enabled`
- `content_files`
- `hide`

## What these keys control

- **`experiment_description`** — Text shown in the "Description" diagnostic tab for each experiment.
- **`projects` / `models` / `experiments` / `ensembles` / `diagnostics` / `sub_diagnostics` / `info_keys`** — Rename labels, control sort order, or hide entries.
- **`default_enabled`** — Set which diagnostics/sub-diagnostics are toggled on by default.
- **`content_files`** — Override plot descriptions or info-box metadata for specific files.
- **`hide`** — Centralized hide lists (global or experiment-scoped) for diagnostics and other entries.

## Common entry fields

For sections that define entities (`projects`, `models`, `experiments`, `ensembles`, `diagnostics`, `sub_diagnostics`, `info_keys`), these fields are supported:

- `label`: display rename (does not change source keys)
- `order`: numeric priority; lower number appears earlier (top/left)
- `hidden`: hide this exact entry

## Hiding

Two equivalent ways to hide entries:

- Inline: `hidden: true` on the entry itself
- Centralized: list under top-level `hide:`

Both are supported. For diagnostics and sub-diagnostics, `hide:` also supports experiment-scoped hiding:

- **Global hide:**
  ```yaml
  hide:
    diagnostics:
      - "Sea Ice"
    sub_diagnostics:
      "Ocean 3D":
        - "Hovmoller"
  ```
- **Scoped hide** (only for specific experiments):
  ```yaml
  hide:
    diagnostics:
      - "Ocean 3D":
          - "climatedt-e25.1/IFS-FESOM/o01m/r1"
  ```
- If the experiment list is empty or omitted, it is treated as a global hide.

## Order behavior

- Sorting is ascending by `order` (e.g. `-10` before `0`, `0` before `10`).
- Items without `order` are placed after items with `order`.
- `Description` is kept at the very top when diagnostic priority sorting is active.

## Example config (full feature sketch)

```yaml
projects:
  climatedt-e25.1:
    label: "ClimateDT E25.1"
    order: 10

models:
  climatedt-e25.1:
    IFS-FESOM:
      label: "IFS-FESOM (HR)"
      order: 1

experiments:
  climatedt-e25.1:
    IFS-FESOM:
      o01m:
        label: "Ocean 0.1deg"
        order: 1

ensembles:
  climatedt-e25.1:
    IFS-FESOM:
      o01m:
        r1:
          label: "Member 1"
          order: 1

diagnostics:
  Info:
    label: "Experiment Info"
    order: 1
  Status:
    label: "Run Status"
    order: 2
  "Ocean 3D":
    order: 20

sub_diagnostics:
  "Ocean 3D":
    "time series":
      label: "Time Series"
      order: 1
    "Hovmoller":
      label: "Hovmoller"
      order: 2

info_keys:
  expid:
    label: "Experiment ID"
    order: 1
  institution:
    label: "Institution"
    order: 2

default_enabled:
  # Diagnostics not listed here default to OFF
  diagnostics:
    - "Description"
    - "Info"
    - "Status"
    - "Ocean 3D"
  sub_diagnostics:
    "Ocean 3D":
      - "time series"

content_files:
  # Override plot description for a specific file
  "climatedt-e25.1/IFS-FESOM/o01m/r1/ocean3d.hovmoller.climatedt-e25_1.IFS-FESOM.o01m.r1.arctic_ocean.png":
    description: "Manual description override for this plot."
  # Override info-box metadata (use "<project>/<model>/<experiment>/<ensemble>/info_keys")
  "climatedt-e25.1/IFS-FESOM/o01m/r1/info_keys":
    expid: "t0kf"
    institution: "My Center"
    note: "Additional free-form info key"

experiment_description:
  climatedt-e25.1:
    IFS-FESOM:
      o01m:
        r1: "Description text shown in the Description diagnostic tab."

hide:
  projects:
    - nextgems4
  models:
    climatedt-e25.1:
      - IFS-NEMO
  experiments:
    climatedt-phase1:
      ICON:
        - ssp370
  ensembles:
    climatedt-e25.1:
      IFS-FESOM:
        o01m:
          - r3
  diagnostics:
    - "Sea Ice"
    - "Ocean 3D":
        - "climatedt-e25.1/IFS-FESOM/o01m/r1"
  sub_diagnostics:
    "Ocean 3D":
      - "Hovmoller"
      - "time series":
          - "climatedt-e25.1/IFS-FESOM/o01m/r1"
  info_keys:
    - "note"
```

## Experiment description shapes

`experiment_description` supports two equivalent styles:

- **Nested:**
  ```yaml
  experiment_description:
    project:
      model:
        experiment:
          ensemble: "text"
  ```
- **Flat slash path:**
  ```yaml
  experiment_description:
    "project/model/experiment/ensemble": "text"
  ```

## Troubleshooting

- **Change not visible after pushing:**
  - The signer caches `/api/descriptions` for ~30 seconds. Wait for the TTL to expire.
- **Entry not matched:**
  - Keys must exactly match the names used in the experiments data (`project/model/experiment/ensemble`).
- **Hide not working:**
  - Both `hidden: true` and `hide:` are valid; check spelling and nesting.
- **Env-specific change visible on all dashboards:**
  - Check that the change is in the env file (e.g. `desp.yaml`), not in `common.yaml`.
