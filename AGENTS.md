# Repository Guide

This repository is a Codex plugin marketplace. Keep contributor instructions here and keep `README.md` focused on
installing and using the published plugins.

## Repository layout

```text
.
├── .agents/plugins/marketplace.json
├── plugins/
│   └── <plugin-name>/
│       ├── .codex-plugin/plugin.json
│       ├── .app.json              # Optional connector mapping
│       ├── .mcp.json              # Optional MCP server configuration
│       ├── assets/                # Optional plugin presentation assets
│       ├── hooks/
│       │   └── hooks.json         # Optional lifecycle hooks
│       └── skills/
│           └── <skill-name>/
│               ├── SKILL.md
│               ├── agents/openai.yaml
│               ├── scripts/       # Optional deterministic helpers
│               ├── references/    # Optional documentation loaded as needed
│               └── assets/        # Optional files copied or used in outputs
├── README.md
└── package.json
```

- `.agents/plugins/marketplace.json` is the marketplace catalog. Each entry points to a plugin under `plugins/`.
- `plugins/<plugin-name>/.codex-plugin/plugin.json` is the plugin manifest and the only place that stores the plugin's
  version.
- Optional plugin-level `.app.json`, `.mcp.json`, `hooks/`, and `assets/` entries provide connector mappings, MCP server
  configuration, lifecycle hooks, and presentation assets. Add only the components the plugin implements.
- `plugins/<plugin-name>/skills/<skill-name>/SKILL.md` contains agent-facing instructions. Its adjacent
  `agents/openai.yaml` contains the skill's user-facing metadata and invocation policy.
- `README.md` contains marketplace installation, plugin installation, and example prompts for users.
- `package.json` contains repository tooling. The root package version is unrelated to plugin versions.

Use lowercase hyphen-case for plugin and skill directory names. A plugin directory name, its manifest `name`, and its
marketplace entry `name` must match.

Keep plugins and skills sorted alphabetically by name wherever they are listed, including the marketplace catalog,
manifest arrays, README sections, installation commands, and example prompts.

## Create a skill

Use the `skill-creator` skill when it is available, then adapt its output to this repository's structure.

1. Choose the parent plugin and create `plugins/<plugin-name>/skills/<skill-name>/`.
2. Add `SKILL.md` with only `name` and `description` in its YAML frontmatter. Make `name` match the skill directory.
   Describe both what the skill does and the requests that should trigger it, then write concise, imperative
   instructions in the body.
3. Add `agents/openai.yaml` with `interface.display_name` and `interface.short_description`. Include
   `interface.default_prompt` only when a reasonable, ready-to-use default is available; omit it when the invocation
   requires user-specific inputs that cannot be inferred. When included, refer to the skill as `$<skill-name>` in the
   default prompt. Set `policy.allow_implicit_invocation` deliberately; the current skills use `false` and require
   explicit invocation.
4. Add `scripts/`, `references/`, or `assets/` only when the skill needs them. Link required resources from `SKILL.md`
   with paths relative to that file, and test any executable scripts.
5. Update the parent `plugin.json` descriptions, capabilities, keywords, or starter prompts when the new skill changes
   the plugin's advertised behavior. Include `interface.defaultPrompt` only when reasonable, ready-to-use defaults are
   available; otherwise omit it. Keep it to at most three short prompts when included.
6. Bump the parent plugin version and update the plugin's description and examples in `README.md` when its user-facing
   behavior changes. README examples must be complete prompts, though users may need to adapt their concrete paths,
   scopes, or other values.

Before finishing, confirm that the skill name and metadata agree, every referenced file exists, and the parent manifest
still describes the combined plugin accurately.

## Create a plugin

Use the `plugin-creator` skill when it is available. Target this repository's `plugins/` directory and repo-local
`.agents/plugins/marketplace.json`; do not create or update a personal marketplace.

1. Create `plugins/<plugin-name>/.codex-plugin/plugin.json`, using an existing plugin manifest as the repository-local
   template. Start a new plugin at version `0.1.0` and provide real values for its description, author, keywords,
   `skills`, and required `interface` metadata. Keep paths relative to the plugin root and beginning with `./`.
2. Add at least one skill under `plugins/<plugin-name>/skills/` by following the skill workflow above.
3. Add an entry to `.agents/plugins/marketplace.json` in alphabetical order. Use `./plugins/<plugin-name>` for
   `source.path`, include both policy fields and a category, and default new plugins to `AVAILABLE` unless they are
   intentionally installed by default:

   ```json
   {
     "name": "<plugin-name>",
     "source": {
       "source": "local",
       "path": "./plugins/<plugin-name>"
     },
     "policy": {
       "installation": "AVAILABLE",
       "authentication": "ON_INSTALL"
     },
     "category": "Productivity"
   }
   ```

4. Add the plugin to the available-plugin list, installation commands, and example prompts in `README.md`.
5. Run the validation and formatting checks described below.

Do not add manifest fields for components that do not exist. In particular, add `apps`, `hooks`, or `mcpServers` only
with their corresponding implementation and configuration files.

## Bump a plugin version

Update `version` in `plugins/<plugin-name>/.codex-plugin/plugin.json`. Use semantic versioning:

- Patch (`0.1.0` to `0.1.1`) for backward-compatible fixes or instruction refinements.
- Minor (`0.1.0` to `0.2.0`) for a new backward-compatible skill or capability.
- Major (`1.0.0` to `2.0.0`) for breaking changes to behavior, configuration, or invocation.

Do not change the root `package.json`, `package-lock.json`, or marketplace entry when bumping a plugin version; those
files do not mirror plugin versions. Make one intentional version bump per release after all changes to that plugin are
complete.

To exercise a locally registered plugin after a version change, reinstall it and start a new Codex session:

```bash
codex plugin add <plugin-name>@aforemendude-skills
```

## Validation

Run the repository formatter after editing Markdown, JSON, or YAML, then verify it is clean:

```bash
npm run format
npm run format:check
```

Also inspect the final diff and verify these cross-file contracts:

- Plugin directory name = `plugin.json` name = marketplace entry name.
- Skill directory name = `SKILL.md` name; any `openai.yaml` default prompt uses `$skill-name`.
- Plugin component and marketplace paths resolve to existing files and directories.
- `README.md` descriptions and prompts reflect the published manifests and skills.
