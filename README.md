# Codex Plugin Marketplace

A repository-backed marketplace for reusable Codex plugins.

## Available plugins

- **Agent Fix** (`agent-fix`) — fixes both style quality (CSS ownership and unused CSS) and unit test quality
  (ownership, coverage, and assertions) within a user-specified scope.
- **Agent Review** (`agent-review`) — reviews explicitly scoped code for correctness, security, maintainability, and
  test setup and configuration, or user-selected Markdown content for correctness and clarity with additional checks for
  prompts and skills. Reports redact secrets and sensitive personal data.

## Add the marketplace to Codex

Install the [Codex CLI](https://developers.openai.com/codex/cli/) and add this GitHub repository as a marketplace:

```bash
codex plugin marketplace add aforemendude/skills
```

Verify that Codex knows about it:

```bash
codex plugin marketplace list
```

The marketplace declares both plugins as installed by default, but Codex may not honor that policy. If needed, install
each plugin manually:

```bash
codex plugin add agent-fix@aforemendude-skills
codex plugin add agent-review@aforemendude-skills
```

Start a new Codex session after installation so the plugin's skills are loaded.

### Use a local clone

For marketplace development, clone the repository and register its root directory:

```bash
git clone git@github.com:aforemendude/skills.git
cd skills
codex plugin marketplace add "$PWD"
codex plugin add agent-fix@aforemendude-skills
codex plugin add agent-review@aforemendude-skills
```

Restart the ChatGPT desktop app after changing or installing a local plugin.

### Update or remove the marketplace

```bash
codex plugin marketplace upgrade aforemendude-skills
codex plugin marketplace remove aforemendude-skills
```

## Skills and example prompts

### Agent Fix

#### `$fix-tests-styles`

`$fix-tests-styles` requires an unambiguous scope such as files, directories, packages, components, a diff, or the whole
repository. It fixes both CSS and unit tests in that scope.

The skill may create, modify, or remove test-support files and edit setup or configuration required by the modified
tests. It does not add or update dependencies. Its concise final report omits change statistics and details readily
available from the diff. It reports checks, unresolved source defects, likely expected work that was not performed
(including test-support refactoring blocked by effects outside the confirmed scope), recommended setup, configuration,
or dependency changes, and other context the user would likely need.

Use `$fix-tests-styles` only in trusted repositories. The skill treats repository content as trusted and runs the
repository's existing build and test commands, which may execute repository-controlled code.

Fix a component's styles and tests:

```text
Use $fix-tests-styles to fix styles and unit tests for the Card component under packages/dashboard/src.
```

Optional opt-ins:

- **Class-name renames and source-ownership moves:** these remain report-only unless the prompt explicitly authorizes
  their corresponding fixes.

  ```text
  Use $fix-tests-styles to fix styles and unit tests under packages/dashboard/src. Also fix inconsistent class-names
  and source-ownership problems in that scope.
  ```

- **Production implementation fixes:** production defects exposed by test updates remain report-only unless separately
  authorized.

  ```text
  Use $fix-tests-styles to fix styles and unit tests under packages/dashboard/src/cards. If the test updates expose a
  production implementation defect in that scope, fix it without changing intended behavior.
  ```

### Agent Review

#### `$review-code`

`$review-code` requires an unambiguous code review scope, such as files, directories, packages, applications, features,
components, a diff or change set, a commit or commit range, or the whole repository. It requires a clean worktree and
writes report-only findings progressively to `CODE_REVIEW_<SCOPE_DESCRIPTION>.md`. Large scopes are split into logical
segments with one report per segment. The reviewer may run focused relevant static checks or tests when they provide
useful evidence, using existing dependencies only.

Test-related findings are limited to dependencies, infrastructure, setup, and configuration. The skill does not review
individual test cases, their fixture data, test logic, assertions, coverage adequacy, or missing test scenarios.

Use `$review-code` only in trusted repositories. The skill treats repository content as trusted and may run the
repository's existing static checks or tests, which may execute repository-controlled code.

Review the whole repository:

```text
Use $review-code to review the whole repository.
```

Review a package's current code:

```text
Use $review-code to review the current code under packages/dashboard.
```

#### `$review-markdown`

`$review-markdown` requires one or more exact Markdown file paths and a review scope, such as complete current contents,
uncommitted changes, a commit or commit range, or named sections or lines. It writes `MARKDOWN_REVIEW.md` by default.

Review one complete file:

```text
Use $review-markdown to review the complete contents of README.md.
```

Review a skill's uncommitted changes:

```text
Use $review-markdown to review the uncommitted changes in plugins/agent-review/skills/review-markdown/SKILL.md.
```

Optional opt-ins:

- **Apply fixes:** reviewed Markdown files are not edited unless the prompt explicitly requests fixes.

  ```text
  Use $review-markdown to review the complete contents of docs/setup.md and fix every finding you can resolve safely.
  ```

- **Choose the output:** provide a report path or request another output mode instead of the default generated Markdown
  report.

  ```text
  Use $review-markdown to review the complete contents of README.md and return the complete review in chat instead of
  writing a report file.
  ```

- **Run additional Markdown checks:** `$review-markdown` runs link verification, web browsing, builds, tests, and live
  model inference only when explicitly requested.

  ```text
  Use $review-markdown to review the complete contents of docs/prompt.md. Also verify its links, browse authoritative
  sources to check its factual claims, run the relevant build and tests, and use live model inference to evaluate the
  prompt's behavior.
  ```

- **Validate external metadata schemas:** metadata review otherwise covers only consistency and staleness within the
  repository.

  ```text
  Use $review-markdown to review the complete contents of plugins/agent-review/skills/review-markdown/SKILL.md and
  perform full validation against the latest authoritative metadata schemas.
  ```
