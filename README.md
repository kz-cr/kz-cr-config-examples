# kz-cr-config-examples

Example [CodeRabbit](https://coderabbit.ai) configurations, written as code.

CodeRabbit can be configured with a YAML file (`.coderabbit.yaml`) instead of clicking through the settings UI. This repository collects ready-to-use examples, grouped by config schema version, so you can copy one, adapt it and apply it to your own repositories.

## Repository layout

| Folder | Schema | Description |
| --- | --- | --- |
| [`v2/`](./v2) | [v2](https://coderabbit.ai/integrations/schema.v2.json) | Example `.coderabbit.yaml` using the v2 schema, with custom pre-merge compliance checks (GovTech IM8, MAS TRM) |
| `v3/` | v3 | Coming soon |

Each folder has its own `README.md` that explains what its config does.

## Using an example

1. Pick the folder for the schema version you want and open its `.coderabbit.yaml`.
2. Apply it in one of two ways:
   - **Check it into your repository:** copy `.coderabbit.yaml` to the root of your repo and commit it. CodeRabbit picks it up automatically, and config changes get reviewed like any other code change.
   - **Paste it into the CodeRabbit UI:** in the [CodeRabbit app](https://app.coderabbit.ai), open your organization or repository settings, switch to the YAML editor and paste the contents.
3. Adjust the settings to suit your team. The commented-out sections in each example are optional extras you can turn on.

A `.coderabbit.yaml` in a repository takes priority over the UI settings. Its `inheritance` key controls whether undefined values fall back to the parent settings.

## References

- [CodeRabbit configuration reference](https://docs.coderabbit.ai/reference/configuration)
- [Configuration inheritance](https://docs.coderabbit.ai/configuration/configuration-inheritance)
