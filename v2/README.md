# CodeRabbit Configuration (v2 schema)

This folder contains an example CodeRabbit configuration written against the **v2 schema**:

- [`.coderabbit.yaml`](./.coderabbit.yaml): the example config

## What is a CodeRabbit config?

A CodeRabbit config is a YAML document that controls how CodeRabbit reviews pull requests. It lets you set up CodeRabbit as code instead of clicking through settings, so the setup can be versioned, reviewed and reused across repositories.

It covers things like:

- **Review behaviour**: review profile (`chill` / `assertive`), which branches and draft PRs to auto-review, and path-specific instructions (`reviews.path_instructions`)
- **Quality gates**: built-in and custom pre-merge checks (`reviews.pre_merge_checks`), plus commit status
- **PR summaries & UX**: walkthroughs, sequence diagrams, auto titles, labels and suggested reviewers
- **Knowledge base**: coding guidelines, linked repositories and web search (`knowledge_base`)
- **Chat**: how CodeRabbit replies in PR conversations (`chat`)
- **Tone**: free-form tone instructions (`tone_instructions`)

The first line of the file points editors at the JSON schema, so you get autocomplete and validation in editors that support the YAML language server (for example VS Code with the Red Hat YAML extension):

```yaml
# yaml-language-server: $schema=https://coderabbit.ai/integrations/schema.v2.json
```

## How to use it

You can apply the config in two ways.

### Option 1: Check it into the repository

1. Copy `.coderabbit.yaml` to the **root** of your repository.
2. Commit it and open a PR. CodeRabbit picks up the file automatically, with no extra setup.

This is the recommended approach because:

- Config changes go through code review like any other change.
- The config sits next to the code it governs, and you get history and rollback for free.
- Different repositories can have different configs.

### Option 2: Paste it into the CodeRabbit UI

If you would rather not commit a file, use the YAML editor in the CodeRabbit web app:

1. Open the [CodeRabbit app](https://app.coderabbit.ai) and go to the **Organization** settings, or to a specific **Repository**'s settings.
2. Switch to the YAML/configuration editor view.
3. Paste the contents of `.coderabbit.yaml`, adjust as needed, and save.

Org-level settings apply to every repository in the organization. Repository-level settings apply only to that repository.

### Precedence and inheritance

When a repository has a `.coderabbit.yaml`, it takes priority over the settings in the UI. The `inheritance` key controls whether settings you don't define in the file fall back to the parent (UI or organization) settings:

```yaml
inheritance: false   # this example: the file is the single source of truth
# inheritance: true  # fill in any undefined values from the parent settings
```

See [Configuration inheritance](https://docs.coderabbit.ai/configuration/configuration-inheritance) for details.

## What this example sets up

- A serious, precise review tone that says whether a change sits on a path that could cause a production outage
- **Custom pre-merge checks** for Singapore regulatory frameworks (**GovTech IM8** and **MAS TRM**). `path_instructions` tell CodeRabbit to tag related findings with `[Govtech-IM8]` / `[MAS-TRM]`, and the checks warn when those tags show up
- Auto-review on all base branches, including draft PRs
- Prompts for AI agents, slop detection, web search and automatic linking of related repositories
- A trimmed-down walkthrough: no poem, no fortunes, no review details
- Commit status that fails on blocking issues, with auto-approvals turned off

The commented-out blocks are optional extras you can turn on: security/compliance and performance checks, agent-instruction file hardening, a high-signal review policy and an executive summary.

## References

- [Configuration reference](https://docs.coderabbit.ai/reference/configuration)
- [v2 JSON schema](https://coderabbit.ai/integrations/schema.v2.json)
