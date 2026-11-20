# Guardrails: protecting CodeRabbit checks and instructions

CodeRabbit configuration lives in the repository, so **anyone who can open a pull request may be able to change the rules that review it**. For example, they could set `enabled: false` on a compliance check, switch its `mode` to `off`, or weaken an instruction. This guide explains how to stop that on GitHub, GitLab, Bitbucket and Azure DevOps.

## The problem

By default (**Auto** mode), CodeRabbit reviews a pull request with the configuration **on the pull request's own branch**, as long as the author is trusted:

| Platform | CodeRabbit treats a PR as trusted when the author… | Can pin the config source? | Reads `.coderabbit/` documents? |
| --- | --- | --- | --- |
| GitHub Cloud | is an owner, member or collaborator, or has Write/Maintain/Admin access | **Yes** | **Yes** |
| GitHub Enterprise Server | same as GitHub Cloud | No (Auto only) | No |
| GitLab | has the Owner, Maintainer or Developer role | No (Auto only) | No |
| Bitbucket Cloud / Data Center | opened it from a branch in the same repository (any non-fork PR) | No (Auto only) | No |
| Azure DevOps | opened it from a branch in the same repository (any non-fork PR) | No (Auto only) | No |

PRs from forks always use the configuration at the base commit.

So, outside GitHub Cloud's pinning option, **a trusted PR that edits the config is reviewed using its own edits**. A CodeRabbit pass on a PR that also changes CodeRabbit config can't be trusted on its own. A human owner has to approve the config change.

## Files to protect

| File | Holds | Relevant on |
| --- | --- | --- |
| `.coderabbit.yaml` / `.coderabbit.yml` | Settings. In v2, also `path_instructions` and `custom_checks` | All platforms |
| `.coderabbit/**` (root **and nested**, e.g. `services/api/.coderabbit/`) | v3 / directory instructions and checks | GitHub Cloud |
| `CODEOWNERS` (wherever your platform keeps it) | Who must approve changes to the files above | All platforms that use one |

Always protect the `CODEOWNERS` file itself. Otherwise someone can remove the rule in the same PR.

## Three layers of defence

1. **CodeRabbit:** stop a PR from changing its own review, and pin settings that must never change.
2. **Your Git platform:** require a designated owner (for example, a compliance team) to approve any change to the files above, and block every path around that requirement.
3. **Process:** validate config in CI, and make the CodeRabbit status a required check.

## Layer 1: CodeRabbit settings (all platforms)

- **Global overrides** (*Review settings → Organization defaults → View mode: Global overrides*): pin the keys teams must not change. Repository files, PR commands and PR descriptions can't override them. Global overrides use **v2 keys**. For example, to lock the status check:

  ```yaml
  reviews:
    review_progress: true
    commit_status: true
    fail_commit_status: true
  ```

  Lists in global overrides are **merged** into the repository's lists rather than replacing them. Before you rely on global overrides to enforce org-wide instructions or checks, confirm how they combine with your schema version (especially v3 documents) with CodeRabbit.

- **Configuration source (GitHub Cloud only):** set it to **Default branch** and turn **off** *Allow configuration changes from trusted PRs*. CodeRabbit then always reads config (and `.coderabbit/` documents) from the default branch, so a PR can't change its own review. Alternatively, use **Custom** to pin a dedicated config branch, and lock that branch to admins. Only organization admins can change this setting.

- Check what a review actually used: comment `@coderabbitai configuration`, and read **Run configuration** in the review info. It shows the source mode, branch and commit. That command doesn't list `.coderabbit/` documents.

## Layer 2: platform controls

### GitHub (Cloud and Enterprise Server)

1. **Add a `CODEOWNERS` file** (in `.github/`, the repo root or `docs/`):

   ```gitignore
   # CodeRabbit configuration: changes need compliance approval
   /.coderabbit.yaml     @your-org/compliance
   /.coderabbit.yml      @your-org/compliance
   .coderabbit/          @your-org/compliance   # matches root and nested folders
   /.github/CODEOWNERS   @your-org/compliance
   ```

   Put these lines **at the bottom** of the file. The last matching pattern wins, so a broader rule further down would otherwise override them.

2. **Protect the default branch** with a branch protection rule or a ruleset:
   - Require a pull request before merging.
   - **Require review from Code Owners.**
   - Dismiss stale approvals when new commits are pushed.
   - Require approval of the most recent push (so someone other than the last pusher approves).
   - Require status checks to pass, and add the **CodeRabbit** check.
   - Don't allow bypassing, or keep the bypass list to break-glass admins only.

3. **GitHub Cloud:** also pin the **Configuration source** (Layer 1) so the PR's own edits don't apply to its review.

4. *Optional, stricter:* on plans with **push rulesets** (GitHub Team and above, private and internal repos), a **Restrict file paths** rule can block pushes that touch `.coderabbit.yaml` or `.coderabbit/**`. Only actors on the ruleset's bypass list can then change them. This applies to every branch, so add your config maintainers to the bypass list.

### GitLab

1. **Add a `CODEOWNERS` file** (in the repo root, `docs/` or `.gitlab/`; GitLab uses the first one it finds). Code Owners requires **GitLab Premium or Ultimate**.

   ```gitignore
   # Section with default owners and 1 required approval
   [CodeRabbit configuration][1] @your-group/compliance
   /.coderabbit.yaml
   /.coderabbit.yml
   /.gitlab/CODEOWNERS
   ```

   GitLab doesn't read `.coderabbit/` documents. On GitLab, instructions and checks live in `.coderabbit.yaml` (v2 `path_instructions` and `custom_checks`).

2. **Protect the default branch** (*Settings → Repository → Protected branches*):
   - Allowed to push and merge: **No one** for direct pushes, and Maintainers for merges.
   - Turn on **Code owner approval** for the branch.

3. **Lock down merge request approvals** (*Settings → Merge requests → Approval settings*):
   - Prevent approval by the author.
   - Prevent approvals by users who add commits.
   - Prevent editing approval rules in merge requests.
   - When a commit is added: remove all approvals.

4. Remember that **Developers count as trusted**, so their MR is reviewed with their own `.coderabbit.yaml`. Code owner approval is the control that catches config edits.

### Bitbucket (Cloud and Data Center)

Bitbucket trusts **every non-fork PR**, so anyone with write access can change config in a PR and have it apply to that PR's review.

1. **Add `.bitbucket/CODEOWNERS`** so the right people are added as reviewers automatically. Bitbucket uses the file **from the PR's target branch**, and rules lower in the file take precedence.

   ```gitignore
   .coderabbit.yaml       @compliance-lead
   .coderabbit.yml        @compliance-lead
   .bitbucket/CODEOWNERS  @compliance-lead
   ```

   The syntax for owner groups differs between editions: Data Center 9.0+ uses reviewer groups, and Cloud uses workspace groups. Check your edition's docs.

2. **Know the gap: Bitbucket's CODEOWNERS adds reviewers but doesn't make their approval mandatory by itself.** Enforce approval with one of these:
   - **Merge checks** (Bitbucket Cloud **Premium** enforces them): minimum approvals, plus *minimum approvals from default reviewers*, with your compliance group as default reviewers.
   - **Data Center:** check your version's merge checks for a code-owner approval option.
   - A **Marketplace app** that adds code-owner approval merge checks, or a **custom merge check** (Forge app on Cloud, plugin on Data Center).

3. **Branch restrictions** on the default branch:
   - Prevent direct pushes, rewriting history and deletion.
   - Allow merges via PR only.
   - Limit who can bypass merge checks.

4. *CI backstop:* a Pipelines step can fail when a PR changes `.coderabbit.yaml` (for example, by diffing against the target branch). Make the build a required merge check, and have a designated admin merge approved config changes.

### Azure DevOps

Azure DevOps has no `CODEOWNERS` file. Use **branch policies** on the default branch instead (*Project settings → Repositories → your repo → Policies → Branch policies*). Azure DevOps also trusts **every non-fork PR**.

1. **Automatically included reviewers:** add your compliance group as a **Required** reviewer, with a **path filter**:

   ```text
   /.coderabbit.yaml; /.coderabbit.yml; /.coderabbit/*; */.coderabbit/*
   ```

   Leave *Allow requestors to approve their own changes* **off**.

2. **Require a minimum number of reviewers:**
   - Turn on *Prohibit the most recent pusher from approving their own changes*.
   - Set *When new changes are pushed* to **Reset all approval votes**.

3. **Build validation:** require the pipeline that checks your config (see Layer 3), and require the **CodeRabbit** status if you use it to gate merges.

4. **Branch security:** deny **Bypass policies when completing pull requests** and **Bypass policies when pushing** for everyone except break-glass admins. Also deny direct pushes.

5. Azure DevOps doesn't read `.coderabbit/` documents. Instructions and checks live in `.coderabbit.yaml`, so that's the file to guard.

## Layer 3: process

- **Validate before merge.** Lint `.coderabbit.yaml` against its JSON schema in CI. On GitHub Cloud, also lint `.coderabbit/**` front matter: allowed fields, `id` format, unique IDs and non-empty bodies. One invalid document makes CodeRabbit reject the whole set.
- **Make the CodeRabbit status required** on the default branch, and pin `fail_commit_status` in global overrides so a repository can't switch it off.
- **Audit regularly.** Use `git log -- .coderabbit.yaml .coderabbit/` to see who changed which rule and when. Treat a disabled compliance check (`enabled: false` or `mode: off`) as a review-worthy event.

## Checklist

| Control | GitHub Cloud | GitHub ES | GitLab | Bitbucket | Azure DevOps |
| --- | :-: | :-: | :-: | :-: | :-: |
| Pin config source to default branch | ✅ | — | — | — | — |
| Global overrides for must-not-change keys | ✅ | ✅ | ✅ | ✅ | ✅ |
| Code-owner / required-reviewer approval on config files | ✅ CODEOWNERS | ✅ CODEOWNERS | ✅ CODEOWNERS (Premium) | ⚠️ CODEOWNERS + merge checks or app | ✅ Path-filtered required reviewers |
| Protect the owners file / policy | ✅ | ✅ | ✅ | ✅ | ✅ (branch security) |
| No direct pushes, no bypass | ✅ | ✅ | ✅ | ✅ | ✅ |
| Approvals reset on new commits | ✅ | ✅ | ✅ | ✅ | ✅ |
| CodeRabbit status required | ✅ | ✅ | ✅ | ✅ | ✅ |
| CI validation of config | ✅ | ✅ | ✅ | ✅ | ✅ |

## References

- CodeRabbit: [Choose the configuration branch](https://docs.coderabbit.ai/configuration/branch), [Enforce settings teams can't change](https://docs.coderabbit.ai/configuration/guides/enforce), [Global overrides](https://docs.coderabbit.ai/configuration/global-overrides), [Instruction and check documents](https://docs.coderabbit.ai/configuration/documents)
- GitHub: [About code owners](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners), [About rulesets](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets)
- GitLab: [Code Owners](https://docs.gitlab.com/user/project/codeowners/), [Protected branches](https://docs.gitlab.com/user/project/repository/branches/protected/)
- Bitbucket: [Code owners (Data Center)](https://confluence.atlassian.com/bitbucketserver/code-owners-1296171116.html)
- Azure DevOps: [Branch policies and settings](https://learn.microsoft.com/en-us/azure/devops/repos/git/branch-policies)
