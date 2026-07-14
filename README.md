# Codex Plugin Marketplace

A repository-backed marketplace for reusable Codex plugins.

## Available plugins

- **Agent Review** (`agent-review`) — reviews user-selected content in explicitly named Markdown files for correctness
  and clarity, with additional checks for prompts and skills.
- **Agent Fix** (`agent-fix`) — reviews and fixes CSS ownership, unused CSS, unit test ownership, and unit test coverage
  across repository packages.

## Add the marketplace to Codex

Install the [Codex CLI](https://developers.openai.com/codex/cli/) and add this GitHub repository as a marketplace:

```bash
codex plugin marketplace add aforemendude/skills
```

Verify that Codex knows about it:

```bash
codex plugin marketplace list
```

Then install a plugin from the marketplace:

```bash
codex plugin add agent-review@aforemendude-skills
codex plugin add agent-fix@aforemendude-skills
```

Start a new Codex session after installation so the plugin's skills are loaded.

### Use a local clone

For marketplace development, clone the repository and register its root directory:

```bash
git clone git@github.com:aforemendude/skills.git
cd skills
codex plugin marketplace add "$PWD"
codex plugin add agent-review@aforemendude-skills
```

Restart the ChatGPT desktop app after changing or installing a local plugin.

### Update or remove the marketplace

```bash
codex plugin marketplace upgrade aforemendude-skills
codex plugin marketplace remove aforemendude-skills
```

## Marketplace layout

```text
.
├── .agents/plugins/marketplace.json
└── plugins/
    ├── agent-review/
    │   ├── .codex-plugin/plugin.json
    │   └── skills/review-markdown/
    │       ├── SKILL.md
    │       └── agents/openai.yaml
    └── agent-fix/
        ├── .codex-plugin/plugin.json
        └── skills/fix-tests-styles/
            ├── SKILL.md
            └── agents/openai.yaml
```

To add another plugin:

1. Create `plugins/<plugin-name>/.codex-plugin/plugin.json`.
2. Place its skills under `plugins/<plugin-name>/skills/`.
3. Append its entry to `.agents/plugins/marketplace.json`, keeping `source.path` relative to the repository root (for
   example, `./plugins/<plugin-name>`).
4. Validate the plugin before publishing it.

See the [Codex plugin documentation](https://learn.chatgpt.com/docs/build-plugins) for plugin manifest and marketplace
details.
