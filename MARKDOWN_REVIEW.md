### Medium — The edit allowlist conflicts with authorized class renames and source-ownership moves

- File: `plugins/agent-fix/skills/fix-tests-styles/SKILL.md:21-26`
- Problematic text or location: Lines 21–22 limit edits to stylesheets, unit tests, and production-file imports or
  references needed to move or remove styles. Lines 23–26 then authorize class-name renames and source-ownership moves
  when separately requested and require every affected production and test reference to be updated.
- Problem: A class-name rename can require production reference edits that are unrelated to moving or removing styles,
  and a source-ownership move necessarily creates or changes production implementation files. Those edits are prohibited
  by the preceding allowlist even after the user gives the additional authorization required by lines 23–26.
- Impact: The agent cannot satisfy both instruction sets. It may refuse an explicitly authorized fix, perform only part
  of it and leave broken references, or violate the declared mutation boundary.
- Recommendation: Add an explicit exception to the allowlist for separately authorized class renames and
  source-ownership moves, including the production source files and references necessary to complete them. Preserve the
  requirement to discover out-of-scope consumers and obtain confirmation before expanding the edit scope.

### Low — Activation metadata omits the supported review mode

- File: `plugins/agent-fix/skills/fix-tests-styles/agents/openai.yaml:2-3`
- Problematic text or location: The display name and short description are “Fix Tests & Styles” and “Fix both CSS and
  unit-test quality.”
- Problem: The user-facing metadata presents the skill as fix-only, while the skill frontmatter, plugin manifest,
  README, and operative instructions support both review and fix modes.
- Impact: Users may not discover or understand the non-mutating review capability, and the activation metadata is stale
  relative to the skill’s behavior.
- Recommendation: Update the display name or, at minimum, the short description to mention both review and fix behavior
  while keeping it concise.
