# kz-cr-config-examples

Example [CodeRabbit](https://coderabbit.ai) configurations, written as code.

CodeRabbit can be configured with a YAML file (`.coderabbit.yaml`) instead of clicking through the settings UI. This repository collects ready-to-use examples, grouped by config schema version, so you can copy one, adapt it and apply it to your own repositories.

## Repository layout

| Folder | Schema | Description |
| --- | --- | --- |
| [`v2/`](./v2) | [v2](https://coderabbit.ai/integrations/schema.v2.json) | Example `.coderabbit.yaml` using the v2 schema, with custom pre-merge compliance checks (GovTech IM8, MAS TRM) |
| [`v3/`](./v3) | [v3](https://coderabbit.ai/integrations/schema.v3.json) (preview) | The `v2/` example migrated to v3: a settings-only `.coderabbit.yaml`, plus instructions and checks as Markdown documents in `.coderabbit/` |

Each folder has its own `README.md` that explains what its config does.

## Guides

- [`GUARDRAILS.md`](./GUARDRAILS.md): how to stop pull requests from weakening CodeRabbit checks and instructions, on GitHub, GitLab, Bitbucket and Azure DevOps

## Attribution

- The `v2/` config, and the `v3/` migration of it, are based on the [Enterprise-style CodeRabbit configuration](https://gist.github.com/iantybo/4c872c62df0c256a839ef7299675cff1) by [iantybo](https://github.com/iantybo).

## Using an example

1. Pick the folder for the schema version you want and open its `.coderabbit.yaml`.
2. Apply it in one of two ways:
   - **Check it into your repository:** copy `.coderabbit.yaml` to the root of your repo and commit it. CodeRabbit picks it up automatically, and config changes get reviewed like any other code change. For **v3**, also copy the `.coderabbit/` directory (instruction and check documents) to the repo root.
   - **Paste it into the CodeRabbit UI:** in the [CodeRabbit app](https://app.coderabbit.ai), open your organization or repository settings, switch to the YAML editor and paste the contents. For **v3**, pasted settings don't include the `.coderabbit/` documents. Those must still be committed to the repository under review.
3. Adjust the settings to suit your team. The optional extras in each example are commented out (v2) or `enabled: false` documents (v3), so you can turn them on as needed.

> [!NOTE]
> **v3 is a limited preview.** Check the [prerequisites](https://docs.coderabbit.ai/configuration/guides/migrate-to-v3#before-you-start) before using it. Adding `version: 3` doesn't enroll your organization.

A `.coderabbit.yaml` in a repository takes priority over the UI settings. Its `inheritance` key controls whether undefined values fall back to the parent settings.

## References

- [CodeRabbit configuration reference](https://docs.coderabbit.ai/reference/configuration)
- [Configuration inheritance](https://docs.coderabbit.ai/configuration/configuration-inheritance)
