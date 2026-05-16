# AGENTS.md

## Project purpose

Central [CodeRabbit](https://www.coderabbit.ai/) configuration baseline for the `vyos` (public) GitHub organisation. `.coderabbit.yaml` here is applied to every `vyos/<repo>` PR review unless the consumer carries its own `.coderabbit.yaml` with `inheritance: true`. Discovery is name-load-bearing — CodeRabbit's [central-configuration](https://docs.coderabbit.ai/configuration/central-configuration) feature looks up the literal repo name `coderabbit` in each org; renaming breaks the integration.

Sibling — **not** twin — of [VyOS-Networks/coderabbit](https://github.com/VyOS-Networks/coderabbit). Several intentional divergences (see "Cross-repo context"); never blindly mirror changes across.

## Tech stack

- One YAML config (`.coderabbit.yaml`) validated against `https://coderabbit.ai/integrations/schema.v2.json`.
- No build, no tests, no scripts. Default branch `production`.

## Build / test / run

No local execution. Edit `.coderabbit.yaml` with `yaml-language-server` enabled to honour the in-file `$schema` directive. CodeRabbit re-reads the baseline on the next PR webhook in any consumer.

## Repository layout

- `.coderabbit.yaml` — the org-wide baseline.
- `CODEOWNERS` — Maintainers team owns all changes.
- `README.md` — operator-facing primer (inheritance contract, per-repo override pattern, rollback).

## Cross-repo context

- Per-repo overrides MUST pin `inheritance: true` (load-bearing). Without it the per-repo file **fully replaces** this baseline; with it CodeRabbit deep-merges (objects merge, arrays union, scalars child-wins).
- **Public-repo edit blindspot:** CodeRabbit ignores `.coderabbit.yaml` changes coming from PRs against public repos — it reads only the base-branch version. PRs that modify this file therefore cannot be self-validated by CodeRabbit. Treat the private sibling's CI as the canary for the same class of bugs.
- **Divergences from [VyOS-Networks/coderabbit](https://github.com/VyOS-Networks/coderabbit):**
  - `early_access: true` here (public-org tolerance for new CodeRabbit features); `false` there.
  - `chat.integrations.jira.usage` and `knowledge_base.jira.usage` are both `disabled` here — the Atlassian OAuth grant is attached to `VyOS-Networks` only (CodeRabbit allows one GitHub-org → Jira-tenant link per install).
  - `knowledge_base.linked_repositories` lists `ansible/ansible` because `vyos.vyos` (Ansible collection) lives in this org; the Networks baseline has no linked repos.
  - Networks `base_branches` adds `git-actions` (used by its `.github` repo); this list does not.

## Conventions

- Default branch: `production`. Visibility: public.
- Commit / PR title format: `T<num>: <description>` (or `scope: T<num>: <description>`); enforced by Mergify central rule from [vyos/mergify](https://github.com/vyos/mergify).
- Org-UI Web dashboard (Global Overrides) MUST stay empty — YAML is the single source of truth. Any setting present in Org-UI should be lifted into this file and the Org-UI editor cleared.
- Any change merged to `production` ships to every consumer's next PR webhook immediately. No staging.

## Notes for future contributors

- Authority: [T8764](https://vyos.dev/T8764) (parent), [T8765](https://vyos.dev/T8765) (provisioning). Confluence: page `847151111`.
- Rollback: `git revert` the offending commit, or set the file body to `{}` — every inheriting repo falls back to CodeRabbit hardcoded defaults on the next webhook.
- Cross-org gotcha: the gen-1 mirror pipeline propagates dotfiles between `vyos/<repo>` and `VyOS-Networks/<repo>` mirrors. For mirrored consumer repos, the per-repo `.coderabbit.yaml` on the `vyos` source is the source of truth for both. The only cross-org-safe value for Jira knobs in a per-repo file is `usage: auto` (self-disables on public, activates on private).
