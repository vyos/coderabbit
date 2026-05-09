# `vyos/coderabbit` — central CodeRabbit baseline

This repository hosts the org-wide [CodeRabbit](https://www.coderabbit.ai/) configuration applied by default to every repository in `vyos/*` that does **not** carry its own `.coderabbit.yaml`.

CodeRabbit's [central-configuration](https://docs.coderabbit.ai/configuration/central-configuration) feature looks up a repo named exactly `coderabbit` in each org and applies its `.coderabbit.yaml` as the default. This is that repo for `vyos/*`.

## Inheritance (load-bearing)

Both this baseline and every per-repo override pin `inheritance: true`. Without it, per-repo files **fully replace** the central. With it, CodeRabbit deep-merges per the [configuration-inheritance docs](https://docs.coderabbit.ai/configuration/configuration-inheritance):

- objects: deep-merged
- arrays: unioned
- scalars: child wins

## Per-repo override

Add a `.coderabbit.yaml` at the root of the consuming repo. The file MUST start with:

```yaml
# yaml-language-server: $schema=https://coderabbit.ai/integrations/schema.v2.json
inheritance: true
```

Restate only the keys you need to deviate; everything else inherits from this baseline.

## Authority

- Spec: [T8764](https://vyos.dev/T8764) (parent), [T8765](https://vyos.dev/T8765) (provisioning).
- Source-of-truth content: parent spec doc referenced from T8764.

## Rollback

`git revert` the offending commit, or set the `.coderabbit.yaml` body to `{}`. Every inheriting repo falls back to CodeRabbit's hardcoded defaults on the next PR webhook.
