# ClimateDT Experiment Descriptions

Human readable descriptions for ClimateDT experiments. Used by the ClimateDT Evaluation Charts (dashboard) to display experiment descriptions.

You only need to edit the `config.yaml` file in this repo to update descriptions in the dashboard. The branches in this repo are mapped to different dashboard environments as follows:

### Branch mapping

| Branch | Dashboard environment |
|---|---|
| `main` | Internal (dev + production) |
| `desp_staging` | DESP staging |
| `desp` | DESP production |

## Workflow

To add or update experiment descriptions, edit the `config.yaml` file on the **main** branch. When you push changes to the remote repository, you should see the changes reflected in the internal and development dashboard environments within ~30 seconds.

Always make changes to the `main` branch. Then, when the changes are verified in the internal environment, you can merge `main` into the `desp_staging` branch, and then create a PR from `desp_staging` to `desp` to promote the changes to the DESP production environment.
