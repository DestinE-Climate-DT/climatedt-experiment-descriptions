# ClimateDT Experiment Descriptions

Runtime configuration for the ClimateDT Dashboard — experiment descriptions and UI overrides.

## How it works

The dashboard backend fetches `config.yaml` from this repo at runtime via GitHub's raw content API, with a 30-second TTL cache. **No dashboard rebuild is needed** when descriptions change.

## Branch mapping

| Branch | Dashboard environment |
|---|---|
| `main` | Internal (dev + production) |
| `desp_staging` | DESP staging |
| `desp` | DESP production |

## Editing

1. Switch to the correct branch for the target environment
2. Edit `config.yaml` (directly on GitHub or via PR)
3. Changes appear in the dashboard within ~30 seconds

## Format

```yaml
experiment_description:
  <project>:
    <model>:
      <experiment>:
        <ensemble>: "Description text here"
```

The hierarchy matches the path structure in the AQUA S3 buckets: `project/model/experiment/ensemble/`.
