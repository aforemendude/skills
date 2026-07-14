---
name: fix-tests-styles
description:
  Fix CSS and unit test ownership, remove unused CSS, and improve unit test coverage. Use only when the user explicitly
  invokes `$fix-tests-styles` or asks to use the fix-tests-styles skill for styles, unit tests, or both.
---

# Scope

Keep changes focused on CSS and unit tests, if there are any issues or bugs with the source files, report them instead
of editing.

Cover all files in the requested scope. If the user does not specify a scope, inspect the entire repository. Exclude
dependency and generated-output directories, such as `node_modules`, `dist`, and `build`.

# Workflow

1. Confirm that there are no uncommitted changes in the repository. If there are, stop and ask the user to commit or
   stash them before proceeding.
2. Identify all files in the requested scope, confirm and run the repository's build and test commands to establish a
   baseline. If any build or test commands fail, stop and report the failure to the user.
3. Review the CSS and unit test rules below, and make any changes needed to fix ownership, remove unused CSS, and
   improve unit test coverage. If there are any issues or bugs with the source files, report them instead of editing.

## CSS Rules

- Keep component styles in the stylesheet with the same basename as the component source, such as `MyComponent.tsx` and
  `MyComponent.css`.
- Keep each component stylesheet limited to selectors used by that component file. Move selectors to the matching
  component stylesheet when ownership is wrong.
- Delete selectors after proving they are unused. Check component markup, generated class strings, conditional class
  branches, string constants, and test fixtures before removing CSS.
- Delete empty component stylesheets and remove their imports.
- Treat `common.css` as a special shared stylesheet. If the entrypoint component is trivial, treat the stylesheet with
  the same basename as the entrypoint component as the shared stylesheet instead. There should only be one such
  stylesheet. Keep it limited to element selectors, pseudo-classes and pseudo-elements attached to global selectors, CSS
  variables, resets, document-level layout, font imports, and other shared global rules. Do not add class or ID
  selectors to it.

## Test Rules

- Keep each unit test file next to, or in the package's established test location for, the source file with the matching
  basename, such as `utils.test.ts` testing `utils.ts` or `MyComponent.test.tsx` testing `MyComponent.tsx`.
- Allow a test file to import shared test setup, fixtures, types, and mocked dependencies, but keep its assertions
  focused on the public contract of the matching source file.
- Move tests for helpers, child components, sibling components, or utilities into the matching test file for the helper,
  child component, sibling component, or utility being tested.
- Require every source file to have a same-basename unit test unless it is a pure type declaration file, a pure constant
  file, or test utility or setup file. Treat `.d.ts` files and files that only export types or interfaces as pure type
  declarations. Treat a file as a pure constant file only when it contains simple exported literals or static
  configuration without branching, functions, derived values, environment reads, side effects, or runtime formatting.
- Keep tests deterministic. Control time, timers, globals, file-system access, child process, network calls, local
  storage, and browser APIs with the repository's test mocks or explicit fixtures.

# Completion

After making changes, confirm that the repository builds and all tests pass. Make any adjustments needed to fix build or
test failures.

Summarize the changes, checks run, and intentionally deferred items. For audit-only work, list actionable findings with
file and line references and do not edit files.
