---
name: fix-tests-styles
description:
  Audit and fix CSS ownership, unused CSS, unit test ownership, and unit test coverage across repository packages. Use
  only when the user explicitly invokes `$fix-tests-styles` or asks to use the fix-tests-styles skill for styles, unit
  tests, or both.
---

# Scope

Keep changes focused on CSS and unit tests unless the user explicitly broadens the task.

- Fix issues by default. If the user explicitly asks for audit-only, review-only, or suggestions-only work, report
  findings without editing files.
- Cover every package in the requested scope. If the user does not name a package, inspect all repository packages that
  contain source, CSS, or unit test files.
- Preserve established repository conventions unless they conflict with an explicit rule in this skill.

# Workflow

1. Identify package source roots, stylesheet imports, component stylesheets, source files, unit test files, and the
   repository's relevant test commands before making changes.
2. Map stylesheet selectors to their owning source files. Move misplaced selectors, remove proven-unused selectors, and
   delete empty stylesheets and imports.
3. Map source files to same-basename unit tests. Move misplaced tests, add missing tests where required, and strengthen
   weak tests with assertions that verify behavior.
4. Run the relevant package checks or tests when feasible. If a full check is too broad, run focused tests for changed
   files and state the limitation.
5. Reinspect changed imports, stylesheets, source files, and tests to catch orphaned files or misplaced coverage.

# CSS Rules

- Keep component styles in the stylesheet with the same basename as the component source, such as `ScheduleTable.tsx`
  and `ScheduleTable.css`.
- Keep each component stylesheet limited to selectors used by that component file. Move selectors to the matching
  component stylesheet when ownership is wrong.
- Delete selectors only after proving they are unused. Check component markup, generated class strings, conditional
  class branches, string constants, and test fixtures before removing CSS.
- Delete empty component stylesheets and remove their imports.
- Treat `common.css` as a special shared stylesheet. Keep it limited to element selectors, pseudo-classes and
  pseudo-elements attached to global selectors, CSS variables, resets, document-level layout, font imports, and other
  shared global rules. Do not add class or ID selectors to it.

During discovery:

- Enumerate all CSS files and every stylesheet import, excluding dependency and generated-output directories.
- Inspect `common.css` files for class and ID selectors as a first-pass signal.
- Confirm multiline selectors, nested selectors, `:is()`, `:where()`, generated class strings, and color values manually
  before changing CSS.

# Test Rules

- Keep each unit test file next to, or in the package's established test location for, the source file with the matching
  basename, such as `time.test.ts` testing `time.ts` or `ServerLogsSection.test.tsx` testing `ServerLogsSection.tsx`.
- Allow a test file to import shared test setup, fixtures, types, and mocked dependencies, but keep its assertions
  focused on the public contract of the matching source file.
- Move tests for helpers, child components, sibling components, or utilities into the matching test file for the helper,
  child component, sibling component, or utility being tested.
- Require every source file to have a same-basename unit test unless it is a pure type declaration file or a pure
  constant file. Treat `.d.ts` files and files that only export types or interfaces as pure type declarations. Treat a
  file as a pure constant file only when it contains simple exported literals or static configuration without branching,
  functions, derived values, environment reads, side effects, or runtime formatting.
- Keep tests deterministic. Control time, timers, globals, file-system access, network calls, local storage, and browser
  APIs with the repository's test mocks or explicit fixtures.

During discovery:

- Enumerate unit test files across the requested scope, excluding dependency and generated-output directories.
- Enumerate implementation files using the repository's supported source extensions, excluding tests and declaration
  files.
- Compare normalized basenames and established test locations before classifying a test as missing or misplaced.

# Completion

Summarize the changes, checks run, and intentionally deferred items. For audit-only work, list actionable findings with
file and line references and do not edit files.
