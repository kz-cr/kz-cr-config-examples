# CodeRabbit Configuration (v3 schema)

This folder contains the [v2 example](../v2) migrated to the **v3 schema**:

- [`.coderabbit.yaml`](./.coderabbit.yaml): the v3 config (settings only)
- [`.coderabbit/instructions/`](./.coderabbit/instructions): path instructions, as Markdown documents
- [`.coderabbit/pre-merge-checks/`](./.coderabbit/pre-merge-checks): custom pre-merge checks, as Markdown documents

> **Attribution:** This config is migrated from the [`v2/`](../v2) example, which is forked from the [Enterprise-style CodeRabbit configuration](https://gist.github.com/iantybo/4c872c62df0c256a839ef7299675cff1) by [iantybo](https://github.com/iantybo).

> [!WARNING]
> **V3 is a limited preview.** It works only for eligible GitHub Cloud organizations on CodeRabbit Cloud. Adding `version: 3`, creating `.coderabbit/` or turning on early access does **not** enroll your organization. Use v3 only after CodeRabbit confirms access. Otherwise, use the [v2 example](../v2).

## What changed in v3

V3 isn't just a version bump. It changes the shape of the configuration:

- **Selected by `version: 3`**, not by the schema comment. V3 treats unknown, misspelled and moved keys as errors at every level of nesting.
- **Settings are regrouped** under `reviews.behavior`, `reviews.auto_review`, `reviews.output`, `reviews.tools` and `fun`.
- **Instructions and checks move out of YAML** into Markdown files with YAML front matter under `.coderabbit/`. A `pre_merge_checks` key anywhere in a v3 file is a configuration error.
- **Some settings are fixed, or managed outside the file.** See the tables below.

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

## Migration map

### Settings that moved

| v2 key | v3 key | Value |
| --- | --- | --- |
| `inheritance` | `inheritance` | `false` |
| `tone_instructions` | `fun.tone_instructions` | unchanged (v3 max 250 chars) |
| `chat.art` | `fun.art` | `false` |
| `reviews.in_progress_fortune` | `fun.in_progress_fortune` | `false` |
| `reviews.profile` | `reviews.behavior.profile` | `chill` |
| `reviews.auto_approvals.enabled` | `reviews.behavior.auto_approvals.enabled` | `false` |
| `reviews.pre_merge_checks.override_requested_reviewers_only` | `reviews.behavior.auto_approvals.override_requested_reviewers_only` | `false` |
| `reviews.commit_status` + `reviews.review_progress` | `reviews.behavior.status.progress_updates` | `true` |
| `reviews.fail_commit_status` | `reviews.behavior.status.fail_on_error` | `true` |
| `reviews.auto_review.*` | `reviews.auto_review.*` | unchanged |
| `reviews.review_details` | `reviews.output.review_details` | `false` |
| `reviews.high_level_summary` + `high_level_summary_in_walkthrough` | `reviews.output.summary.placement` | `walkthrough` |
| `reviews.high_level_summary_instructions` | `reviews.output.summary.instructions` | still commented out |
| `reviews.collapse_walkthrough` | `reviews.output.walkthrough.collapse` | `true` |
| `reviews.changed_files_summary` | `reviews.output.walkthrough.changed_files_summary` | `true` |
| `reviews.review_status` | `reviews.output.walkthrough.review_status` | `true` |
| `reviews.related_issues` / `related_prs` | `reviews.output.walkthrough.related_issues` / `related_prs` | `true` |
| `reviews.suggested_labels` + `auto_apply_labels` | `reviews.output.metadata.labels.mode` | `suggest` |
| `reviews.suggested_reviewers` + `auto_assign_reviewers` | `reviews.output.metadata.reviewers.mode` | `suggest` |

Empty v2 lists and maps (`labeling_instructions`, `mutually_exclusive_groups`, `suggested_reviewers_instructions`) were dropped because they match the v3 defaults.

### Settings that became Markdown documents

| v2 entry | v3 document | Status |
| --- | --- | --- |
| `path_instructions` (GovTech IM8) | `instructions/govtech-im8.md` | enabled |
| `path_instructions` (MAS TRM) | `instructions/mas-trm.md` | enabled |
| commented-out agent-file hardening instruction | `instructions/agent-instructions-security.md` | `enabled: false` |
| commented-out high-signal review instruction | `instructions/high-signal-review.md` | `enabled: false` |
| `custom_checks` (Govtech IM8 Compliance) | `pre-merge-checks/govtech-im8-compliance.md` | `mode: warning` |
| `custom_checks` (MAS TRM Compliance) | `pre-merge-checks/mas-trm-compliance.md` | `mode: warning` |
| commented-out security/audit/regulatory check | `pre-merge-checks/regulatory-control-integrity.md` | `enabled: false` |
| commented-out performance/complexity check | `pre-merge-checks/performance-complexity.md` | `enabled: false` |

The optional extras that were commented out in v2 are kept as **disabled documents**. To turn one on, set `enabled: true` in its front matter, or delete the `enabled` line.

### Settings that were removed

| v2 key | Why it's gone in v3 |
| --- | --- |
| `reviews.poem`, `reviews.sequence_diagrams`, `reviews.estimate_code_review_effort`, `reviews.assess_linked_issues` | Fixed **off** in v3 |
| `reviews.enable_prompt_for_ai_agents`, `chat.auto_reply` | Fixed **on** in v3 |
| `reviews.pre_merge_checks.title` / `description` / `issue_assessment` / `docstrings` | Built-in checks are off in v3 (they were already off in the v2 example) |
| `knowledge_base` (automatic linking, web search, code guidelines) | Managed in the web interface, not the file |
| `reviews.finishing_touches`, `reviews.post_merge_actions` | Managed in the web interface, not the file |
| `reviews.slop_detection`, `reviews.auto_title_placeholder`, `reviews.auto_title_instructions` | No v3 key. Check the resolved value with `@coderabbitai configuration` |

**Re-create in the UI:** the v2 example turned on Knowledge Base automatic linking and web search. In v3, an admin has to configure these in the CodeRabbit web interface.

## References

- [Configuration V3 reference](https://docs.coderabbit.ai/reference/configuration-v3)
- [Migrate configuration to V3](https://docs.coderabbit.ai/configuration/guides/migrate-to-v3)
- [Instruction and check documents](https://docs.coderabbit.ai/configuration/documents)
- [v3 JSON schema](https://coderabbit.ai/integrations/schema.v3.json)
