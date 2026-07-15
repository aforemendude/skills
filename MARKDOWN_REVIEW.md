### High — Missing inputs default to repository-wide edits

- **Location:** `plugins/agent-fix/skills/fix-tests-styles/SKILL.md:10-18`
- **Problematic text:** “If the operation is unspecified, default to fixes; if the category is unspecified, default to
  both CSS and unit tests” and “If the user does not specify a scope, inspect the entire repository.”
- **Problem:** An explicit skill invocation with no further detail becomes authorization to modify both CSS and tests
  across the entire repository. The user is not required to choose review versus mutation, select a category, or bound
  the file/package scope before edits begin.
- **Impact:** A terse or exploratory invocation can trigger broad, surprising changes that exceed the user's likely
  intent and are difficult to review safely.
- **Recommendation:** Require explicit confirmation of fix mode and a concrete scope before editing. If defaults are
  necessary, default to read-only review and a bounded scope inferred from the user's named files or current task; ask
  before expanding to repository-wide fixes.
