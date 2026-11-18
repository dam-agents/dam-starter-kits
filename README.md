# Starter kits

A starter kit catalog. A kit is a `kit.yaml` describing what an agent needs to
do one job — the connections it asks for, the schedules it creates, the skills
it brings and the definition repository it works from — so that creating that
agent is one click instead of a checklist.

## Layout

```
catalog.yaml            the index: which kits this catalog offers
kits/<id>/kit.yaml      a kit with no repository of its own
```

An entry either names a repository that carries its own `kit.yaml` — the usual
shape, where one commit pins the kit, the definition it describes and the
skills bundled with it together — or a directory here, for a kit with nothing
of its own to sit beside.

## Pointing an install at this catalog

```yaml
starterKits:
  catalogs:
    - name: curated
      url: https://github.com/dam-agents/dam-starter-kits
```

Kits are addressed `<catalog>/<kit>`, so this one's code review agent is
`curated/code-guardian`. Two catalogs may ship a kit of the same id without one
hiding the other.

The platform re-reads every catalog periodically, so **adding a kit here needs
no redeploy**. It resolves each entry to a commit as it reads, and an agent
records the commit it was created from — a moving catalog still yields exact,
reproducible kits.

## Adding a kit

Anyone can propose a kit for this catalog with a pull request. Once it is
merged, the kit is on the **Starter Kits** page for every user of the
deployments that read this catalog.

1. Add `kit.yaml` to the root of the repository the kit describes. The
   repository must be public on github.com, and so must any repository the
   kit seeds the agent from. Before you open the pull request, try the kit:
   paste the repository's link into **Import from link** on **Starter Kits**
   and create an agent from it. That kit is used only by you.
2. List the repository at the end of `kits:` in `catalog.yaml`. For a kit you
   built, name yourself as its `publisher`:

   ```yaml
   kits:
     # ...existing kits...
     - url: https://github.com/<you>/<repo>
       publisher:
         name: Your Name
         url: https://github.com/<you>
   ```

   - `url` is the kit's repository. Don't put a branch or a version in it;
     add `ref` to pin one.
   - `publisher.name` is shown under the kit's title as **By Your Name**, so
     users can tell your kit from the DAM team's, which read **By DAM**. It
     can be a person or a team, up to 80 characters.
   - `publisher.url` is optional and must start with `https://`. In the kit's
     window, the name links to it.

   A kit with no repository of its own goes in `kits/<id>/kit.yaml` here
   instead, listed as `- path: kits/<id>`; its `id` must match the directory
   name, and CI validates it against `kit.schema.json`.
3. Open a pull request. Say what the kit does and who it's for.

> [!IMPORTANT]
> An entry without `publisher` is shown as a kit from the DAM team. Leave it
> out only for the team's own kits. Only this catalog says who publishes a
> kit; `kit.yaml` has no field for it, so a kit can't claim to be the team's.

After the merge, the kit appears at the platform's next catalog refresh,
within about 10 minutes. To update it, push to the kit's repository: new
agents use the new commit after the next refresh. To change your name or
link, or remove the kit, open a pull request that edits or deletes its entry.

For the full guide, see
[Create your own starter kit](https://dam-agents.github.io/docs/guides/create-starter-kit/#share-it).

Every `kit.yaml` carries a `# yaml-language-server: $schema=` line pointing at
`kit.schema.json` here, and so does this catalog, so an editor with the YAML
extension validates and autocompletes them as you type — wherever the file
lives.

`kit.schema.json` and `catalog.schema.json` are **generated** from the
platform's own schema — they are the same definition the platform validates
against when it reads a kit, not a copy maintained by hand.

## What a kit may declare

`kit.schema.json` is the complete list. In short:

| Field | Meaning |
|---|---|
| `schemaVersion` | Always `v1` |
| `id`, `name`, `description`, `category` | Identity and how it is listed. Category is one of `software`, `productivity`, `knowledge`, `research` |
| `tagline` | A short line for the catalog card, shown instead of `description` |
| `icon` | Icon name for the catalog card |
| `avatar` | The new agent's avatar: a built-in character (`shield`), an emoji (`"emoji:🛡️"`), or an image in the kit repository (`{path: avatar.png}`) |
| `video`, `docsUrl` | Links to a walkthrough video, shown in the kit's window, and to the kit's documentation |
| `image` | The kit brings its own agent image, instead of running on a harness |
| `harnesses` | Harness families it can run on, when it brings no image: `claude-code`, `codex`, `pi`, `bob`, `copilot`, `hermes`. The agent starts on the first one |
| `providers`, `defaultModel` | The model providers the job works best on, and the model the agent must run on. The user is never asked to pick a provider |
| `resources` | Workspace disk (`storage`) when the work needs more than the default, with a `note` saying why. CPU and memory are not a kit's to set |
| `egressPreset` | The agent's web access: `none`, `trusted` (the default: npm, PyPI, GitHub, Anthropic and similar) or `all`. The owner can change it after create |
| `egressRules` | Host rules added on top of the preset, in the shape a hand-written rule takes: `host`, optional `port`, `method`, `pathPattern`, and `verdict` (`allow` by default, or `deny`) |
| `seed` | The definition repository the agent's workspace starts from: a `url`, or `self: true` for the kit's own repository, with an optional `ref` |
| `install` | A command run in the agent's workspace when it is created |
| `onboarding` | What the agent's first session opens with. Leave it out and the agent follows the definition's `ONBOARDING.md` when the kit has a seed, or asks the user what to work on. `false` opens no onboarding session |
| `bundledSkills.path` | Directory in the definition holding skills. Names and descriptions are read from each `SKILL.md`, never copied here |
| `skills` | Skills from other repositories, installed when the agent is created |
| `connections` | What the agent needs access to, and whether it is required |
| `requireConnectionAddress` | When on, a connection's credential is added only to requests that name that connection |
| `channels` | Where the team can talk to it: `slack` or `telegram` |
| `schedules` | Recurring work, created with the author's defaults. A schedule may carry a `precheck` — a shell command run in the agent's workspace before each fire, where exit 0 runs the task and exit 1 skips that occurrence without waking a model |
| `startupItems` | Programs the agent runs at every boot |
| `knowledgeBase.shareRoots` | The workspace paths a knowledge-base kit publishes |
| `env` | Fixed environment variables |
