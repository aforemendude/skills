# Codex Plugin Marketplace

A repository-backed marketplace for reusable Codex plugins.

## Available plugins

- **Agent Fix** (`agent-fix`) — reviews and fixes CSS ownership, unused CSS, unit test ownership and coverage, and
  assertion quality across repository packages.
- **Agent Review** (`agent-review`) — reviews user-selected content in explicitly named Markdown files for correctness
  and clarity, with additional checks for prompts and skills.

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

## Example prompts

### Agent Review

```text
Use $review-markdown to review the complete contents of README.md.
```

```text
Use $review-markdown to review the uncommitted changes in plugins/agent-review/skills/review-markdown/SKILL.md.
```

### Agent Fix

```text
Use $fix-tests-styles to review CSS and unit tests across this repository.
```

```text
Use $fix-tests-styles to fix CSS ownership, unit test coverage, and assertion quality in packages/dashboard.
```

See the [Codex plugin documentation](https://learn.chatgpt.com/docs/build-plugins) for plugin manifest and marketplace
details.
