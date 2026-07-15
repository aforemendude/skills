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
