# CodeRabbit Configuration (v3 schema)

This folder contains an example CodeRabbit configuration written against the **v3 schema**:

- [`.coderabbit.yaml`](./.coderabbit.yaml): the v3 config (settings only)
- [`.coderabbit/instructions/`](./.coderabbit/instructions): review instructions, as Markdown documents
- [`.coderabbit/pre-merge-checks/`](./.coderabbit/pre-merge-checks): custom pre-merge checks, as Markdown documents
- [`CHANGELOG.md`](./CHANGELOG.md): how this example was migrated from [v2](../v2), including what moved, what changed and what was removed

> **Attribution:** This config is migrated from the [`v2/`](../v2) example, which is forked from the [Enterprise-style CodeRabbit configuration](https://gist.github.com/iantybo/4c872c62df0c256a839ef7299675cff1) by [iantybo](https://github.com/iantybo).

> [!WARNING]
> **V3 is a limited preview.** It works only for eligible GitHub Cloud organizations on CodeRabbit Cloud. Adding `version: 3`, creating `.coderabbit/` or turning on early access does **not** enroll your organization. Use v3 only after CodeRabbit confirms access. Otherwise, use the [v2 example](../v2).

## How a v3 config is structured

A v3 config has two parts:

1. **`.coderabbit.yaml`** holds **settings only**: review behaviour, auto-review, output and the "fun" options. It must start with `version: 3`. Unknown or misspelled keys are errors at every level.
2. **`.coderabbit/`** holds **Markdown documents** with YAML front matter:
   - `instructions/` shapes review feedback for matching files. Instructions don't produce a check result.
   - `pre-merge-checks/` defines conditions checked against the PR, each with a reported pass or fail result.

V3 has no `path_instructions` or `pre_merge_checks` keys in YAML. That content lives only in these documents.

## How to use it

Copy **both** the file and the directory to the **root** of your repository:

```
your-repo/
├── .coderabbit.yaml
└── .coderabbit/
    ├── instructions/
    └── pre-merge-checks/
```

The `.coderabbit/` documents must live in the repository under review. CodeRabbit doesn't load them from a central configuration repository. Your organization's web interface settings are managed separately, and changing the file doesn't migrate them.

To check the result, open a test PR and comment `@coderabbitai configuration` to confirm that the v3 source was used. That command doesn't show whether the `.coderabbit/` documents loaded, so also read the review to confirm that the instructions and checks applied.

## Writing instructions and checks

### Each file is one document

Every `.md` file has a single front matter block with one `id`, one scope (`paths` / `exclude`) and one `enabled` flag, so **each file defines one instruction or one check**.

```markdown
---
id: api-security            # identity: up to 50 chars, letters/digits/._-
name: API security          # optional display name, 1–50 chars
paths:                      # optional; omit to match every path in scope
  - "src/api/**"
exclude:                    # optional; exclusions beat paths
  - "**/*.test.ts"
enabled: true               # optional; false turns the document off
---

Check that every new endpoint validates its input and requires authentication.
```

Checks also accept `mode` (`off`, `warning` or `error`; default `warning`) and `report` (`platform` or `coderabbit`; default `platform`).

### Recommended convention: one file per concern and scope

- **Group rules that share a scope and purpose** in one file. For example, `high-signal-review.md` holds four related bullets that all apply to `**/*`.
- **Use separate files when:**
  - **The scope differs**, for example rules for `src/api/**` versus `db/migrations/**`. One file has only one `paths` list.
  - **You want to toggle rules independently.** `enabled` applies to the whole document, so you can't turn off one rule inside a combined file.
  - **You may override a rule per package.** A nested `.coderabbit/` (for example `services/api/.coderabbit/instructions/`) with the same `id` replaces the parent document for that directory, or turns it off with `enabled: false`. Separate IDs let you override one rule without touching the others.
- **Name files after their `id`** (for example `id: mas-trm` → `mas-trm.md`). CodeRabbit identifies documents by `id`, not file name, but matching names make files easy to find.
- **Pair instructions with their checks.** In this example, each compliance framework has an instruction that tags findings (`govtech-im8.md`, `mas-trm.md`) and a matching check that reports on those tags (`govtech-im8-compliance.md`, `mas-trm-compliance.md`).
- **Keep optional rules as disabled documents** (`enabled: false`) rather than deleting them, so they're easy to turn on later.

### Rules and limits

- `id` must be unique per kind (instruction or check) and scope. A duplicate is an error, even across different file names.
- Unknown front matter fields and duplicate YAML keys are errors. One invalid document rejects the whole set.
- An enabled document needs a non-empty body.
- Patterns are relative to the directory that contains `.coderabbit/`. They can't be absolute or contain `..`.
- Subfolders inside `instructions/` and `pre-merge-checks/` are allowed for organization, and they don't create new scopes. `README.md` files are ignored.
- Limits: 100 documents, 2 MiB total, 20,000 characters per instruction body, 10,000 characters per check body.

## What this example sets up

**Settings (`.coderabbit.yaml`)**
- A serious, precise review tone that says whether a change sits on a path that could cause a production outage
- `chill` profile, auto-review on all base branches including drafts
- Summary inside the walkthrough, with a collapsed walkthrough that covers changed files, review status and related issues and PRs
- Suggested (not auto-applied) labels and reviewers
- Progress reporting on, with a failing status on errors; auto-approvals off

**Documents (`.coderabbit/`)**

| Document | Kind | Status |
| --- | --- | --- |
| `govtech-im8.md` | instruction | enabled: tags findings with `[Govtech-IM8]` |
| `mas-trm.md` | instruction | enabled: tags findings with `[MAS-TRM]` |
| `govtech-im8-compliance.md` | check | `warning`: fails if any finding is tagged `[Govtech-IM8]` |
| `mas-trm-compliance.md` | check | `warning`: fails if any finding is tagged `[MAS-TRM]` |
| `agent-instructions-security.md` | instruction | disabled: hardening for agent instruction files |
| `high-signal-review.md` | instruction | disabled: blocking or high-confidence issues only |
| `regulatory-control-integrity.md` | check | disabled: security, audit and regulatory controls |
| `performance-complexity.md` | check | disabled: performance and algorithmic complexity |

To turn on a disabled document, set `enabled: true` in its front matter, or delete the `enabled` line.

## References

- [Configuration V3 reference](https://docs.coderabbit.ai/reference/configuration-v3)
- [Instruction and check documents](https://docs.coderabbit.ai/configuration/documents)
- [Migrate configuration to V3](https://docs.coderabbit.ai/configuration/guides/migrate-to-v3)
- [v3 JSON schema](https://coderabbit.ai/integrations/schema.v3.json)
