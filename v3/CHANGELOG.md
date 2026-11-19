# Changelog: v2 → v3 migration

This file records how the [v2 example](../v2) was migrated to the v3 schema, following the [V3 reference](https://docs.coderabbit.ai/reference/configuration-v3) and the [migration guide](https://docs.coderabbit.ai/configuration/guides/migrate-to-v3).

## What changed in v3

V3 isn't just a version bump. It changes the shape of the configuration:

- **Selected by `version: 3`**, not by the schema comment. V3 treats unknown, misspelled and moved keys as errors at every level of nesting.
- **Settings are regrouped** under `reviews.behavior`, `reviews.auto_review`, `reviews.output`, `reviews.tools` and `fun`.
- **Instructions and checks move out of YAML** into Markdown files with YAML front matter under `.coderabbit/`. A `pre_merge_checks` key anywhere in a v3 file is a configuration error.
- **Some settings are fixed, or managed outside the file.** See the tables below.

## Settings that moved

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

## Settings that became Markdown documents

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

The instruction and check text is copied word for word from v2. The optional extras that were commented out in v2 are kept as **disabled documents**.

## Settings that were removed

| v2 key | Why it's gone in v3 |
| --- | --- |
| `reviews.poem`, `reviews.sequence_diagrams`, `reviews.estimate_code_review_effort`, `reviews.assess_linked_issues` | Fixed **off** in v3 |
| `reviews.enable_prompt_for_ai_agents`, `chat.auto_reply` | Fixed **on** in v3 |
| `reviews.pre_merge_checks.title` / `description` / `issue_assessment` / `docstrings` | Built-in checks are off in v3 (they were already off in the v2 example) |
| `knowledge_base` (automatic linking, web search, code guidelines) | Managed in the web interface, not the file |
| `reviews.finishing_touches`, `reviews.post_merge_actions` | Managed in the web interface, not the file |
| `reviews.slop_detection`, `reviews.auto_title_placeholder`, `reviews.auto_title_instructions` | No v3 key. Check the resolved value with `@coderabbitai configuration` |

**Re-create in the UI:** the v2 example turned on Knowledge Base automatic linking and web search. In v3, an admin has to configure these in the CodeRabbit web interface.
