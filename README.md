# Starter kits

A starter kit catalog. A kit is a `kit.yaml` describing what an agent needs to
do one job — the connections it asks for, the schedules it creates, the skills
it brings and the definition repository it works from — so that creating that
agent is one click instead of a checklist.

## Layout

```
catalog.yaml            the index: which kits this catalog offers
kits/<id>/kit.yaml      one kit
```

## Pointing an install at this catalog

```yaml
starterKits:
  catalogs:
    - name: curated
      url: https://github.com/dam-agents/dam-starter-kits
```

Kits are addressed `<catalog>/<kit>`, so this one's code reviewer is
`curated/code-reviewer`. Two catalogs may ship a kit of the same id without one
hiding the other.

The platform re-reads every catalog periodically, so **adding a kit here needs
no redeploy**. It resolves each entry to a commit as it reads, and an agent
records the commit it was created from — a moving catalog still yields exact,
reproducible kits.

## Adding a kit

1. Add `kits/<id>/kit.yaml`.
2. List it in `catalog.yaml`.
3. Open a pull request. CI validates every kit against `kit.schema.json`.

A kit's `id` must match its directory name. Both YAML files carry a
`# yaml-language-server: $schema=` line, so an editor with the YAML extension
validates and autocompletes them as you type.

`kit.schema.json` and `catalog.schema.json` are **generated** from the
platform's own schema — they are the same definition the platform validates
against when it reads a kit, not a copy maintained by hand.

## What a kit may declare

| Field | Meaning |
|---|---|
| `id`, `name`, `description`, `category` | Identity and how it is listed. Category is one of `software`, `productivity`, `knowledge`, `research` |
| `icon` | Icon name for the catalog card |
| `image` | The kit brings its own agent image, instead of running on a harness |
| `harnesses` | Harness families it can run on, when it brings no image |
| `resources` | CPU and memory limits and workspace disk, when it needs more than the default |
| `seed` | The definition repository the agent clones during onboarding |
| `bundledSkills.path` | Directory in the definition holding skills. Names and descriptions are read from each `SKILL.md`, never copied here |
| `skills` | Skills from other repositories, installed when the agent is created |
| `connections` | What the agent needs access to, and whether it is required |
| `channels` | Where the team can talk to it |
| `schedules` | Recurring work, created with the author's defaults |
| `env`, `hibernationTimeoutMin`, `parameters` | Fixed environment, idle behaviour, and the values onboarding will ask the user for |
