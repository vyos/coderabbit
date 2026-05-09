# AGENTS.md — `vyos/coderabbit`

## Project purpose

Org-wide central CodeRabbit baseline for `vyos/*`. CodeRabbit's [central-configuration](https://docs.coderabbit.ai/configuration/central-configuration) feature looks up a repo named exactly `coderabbit` in each org and auto-applies its `.coderabbit.yaml` to every other repo in that org which does not carry its own. This repo is configuration-only — no build artifacts, no Actions, no code.

Public so anyone can audit how AI code review is configured for the public org.

## Tech stack

- Single YAML file: `.coderabbit.yaml` (CodeRabbit schema v2).
- `README.md` (purpose + override instructions).
- `CODEOWNERS` (`@vyos/maintainers`).

## Build / test / run

No build, no tests, no runtime. Edits to `.coderabbit.yaml` take effect on the next PR webhook in any inheriting repo (ten-second propagation in practice; CodeRabbit's org-level cache may take up to ~15 min to refresh on first config publish).

## Repository layout

- `.coderabbit.yaml` — central baseline. Pins `inheritance: true` so per-repo overrides deep-merge instead of fully replacing.
- `README.md` — purpose, inheritance model, override pattern, rollback.
- `CODEOWNERS` — Maintainers.

## Inheritance model (load-bearing)

Every per-repo override file MUST also pin `inheritance: true`. Without it, the override fully replaces this baseline. With it, CodeRabbit deep-merges per the [configuration-inheritance docs](https://docs.coderabbit.ai/configuration/configuration-inheritance):

- objects: deep-merged
- arrays: unioned
- scalars: child wins

Override files restate only the keys that should deviate; everything else inherits.

## Conventions

- Commit / PR title format: `component: T12345: description`. Phorge task ID at https://vyos.dev mandatory.
- Default branch: `production`.
- Schema: pin `# yaml-language-server: $schema=https://coderabbit.ai/integrations/schema.v2.json` at the top of every config file in the fleet (central + per-repo overrides).
- Tone: `tone_instructions` is generic on this public baseline. Internal-product-specific tone goes in `VyOS-Networks/coderabbit` (private).

## Cross-repo context

- Sibling: `VyOS-Networks/coderabbit` (private, same baseline + `git-actions` added to `base_branches`).
- Authority: [T8764](https://vyos.dev/T8764) (parent rollout), [T8765](https://vyos.dev/T8765) (this repo's provisioning).
- Per-repo overlays/overrides are deployed as the consuming repo's own `.coderabbit.yaml` — see spec doc referenced from T8764 for the overlay catalogue (Python, OCaml, C/C++, Perl, shell, TypeScript, Ansible, Go, PHP, Terraform, docs, workflow).

## Rollback

`git revert` the offending commit, or set `.coderabbit.yaml` body to `{}`. Either makes every inheriting repo fall back to CodeRabbit's hardcoded defaults on the next PR webhook.
