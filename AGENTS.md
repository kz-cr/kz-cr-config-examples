# AGENTS.md

Guidance for AI coding agents (and humans) contributing to this repository.

## What this repo is

A collection of example [CodeRabbit](https://coderabbit.ai) configurations (`.coderabbit.yaml`), grouped by config schema version. The repo contains no application code. Its content is YAML configs and the Markdown docs that explain them.

## Layout

```
.
├── README.md            # Repo overview + table of example folders
├── AGENTS.md            # This file
├── CLAUDE.md            # Imports AGENTS.md for Claude Code
├── v2/                  # Examples using the v2 schema
│   ├── .coderabbit.yaml
│   └── README.md
└── v3/                  # Examples using the v3 schema (preview)
    ├── .coderabbit.yaml # Settings only
    ├── .coderabbit/     # Instructions and checks as Markdown documents
    │   ├── instructions/
    │   └── pre-merge-checks/
    ├── CHANGELOG.md     # How the example was migrated from v2
    └── README.md
```

In v3, path instructions and custom pre-merge checks are not YAML keys. They are Markdown files with front matter under `.coderabbit/`. See [Instruction and check documents](https://docs.coderabbit.ai/configuration/documents).

- One top-level folder per schema version (`v2/`, `v3/`, …).
- Each folder holds a `.coderabbit.yaml` and a `README.md` that explains it.
- If a version needs several examples, use subfolders (for example `v2/compliance-sg/`). Each subfolder gets its own `.coderabbit.yaml` and `README.md`.

## Rules for config files

1. **Don't change configs you were given.** When a user supplies a config (from a gist, a URL or pasted text), copy it byte-for-byte. Don't reformat it, re-indent it, fix typos, reorder keys or strip comments, and don't "improve" it unless you're explicitly asked to. Download raw content (for example the gist's raw URL) rather than retyping it.
2. **Keep the schema header** on the first line so editors can validate the file:
   ```yaml
   # yaml-language-server: $schema=https://coderabbit.ai/integrations/schema.v2.json
   ```
   Use the schema URL that matches the folder's version.
3. **Only use keys that exist in the schema.** Don't invent configuration options. When unsure, check the [configuration reference](https://docs.coderabbit.ai/reference/configuration) or the JSON schema.
4. **Keep optional extras commented out** rather than deleting them. The commented blocks are part of what makes these files useful as examples.
5. **Never include secrets**, customer names, internal URLs or org-specific identifiers.

### Validating

To check a config against its schema, use any JSON Schema validator. For example, with [check-jsonschema](https://github.com/python-jsonschema/check-jsonschema):

```sh
check-jsonschema --schemafile https://coderabbit.ai/integrations/schema.v2.json v2/.coderabbit.yaml
```

## Rules for documentation

- When you add or rename an example folder, **update the table in the root `README.md`**.
- Each folder's `README.md` should cover:
  - which schema version it uses
  - what a CodeRabbit config is and the two ways to apply it (commit to the repo root, or paste into the CodeRabbit UI's YAML editor)
  - what this specific example sets up, including what the commented-out sections enable
- Describe only what the config actually does. Don't claim behaviour you haven't confirmed in the config or the CodeRabbit docs.
- Link to official docs (`docs.coderabbit.ai`) instead of copying large parts of them.

## Git workflow

- Work on a feature branch and open a pull request against `main`. Don't push directly to `main` unless the maintainer asks you to.
- Keep commits focused: one example or one doc change per commit where practical.
- Write commit messages in the imperative mood (for example, "Add v3 compliance example").
- Only commit or push when you're asked to.
