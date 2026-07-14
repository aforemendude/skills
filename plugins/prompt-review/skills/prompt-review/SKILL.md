---
name: prompt-review
description:
  Review recent changes in explicitly named Markdown inference prompt files under server/prompts for correctness,
  safety, clarity, typos, grammar, awkward wording, and inconsistencies within or across the named files. Use only when
  the user explicitly invokes `$prompt-review` or asks to use the prompt-review skill and provides one or more exact
  prompt file paths under server/prompts.
---

# Prompt Review

## Requirements

- Require one or more exact file paths under `server/prompts`. If any target is missing or ambiguous, ask for the
  specific file before reviewing.
- Review without editing unless the user explicitly asks for fixes.
- Focus on the most recent changes unless the user specifies another revision, range, or scope:
  1. Review current staged, unstaged, and untracked changes affecting the named files.
  2. If the named files have no current changes, review their changes in the most recent commit that touched at least
     one of them.
- Read every named file in full so changed wording can be checked against its surrounding content and the other named
  files. Keep findings focused on the selected changes; cite unchanged text only when it establishes an inconsistency or
  explains impact.
- Do not inspect placeholder composition or placeholder contracts unless the user specifically requests that check.
- Do not run live inference or web searches merely to review a prompt.

## Workflow

1. Confirm that every named target is a file under `server/prompts`.
2. Establish the review baseline using the user-specified scope or the default change-selection rules above. State the
   resulting diff or commit in the review.
3. Read the selected changes and each complete target file.
4. Review the changed text for:
   - typos, grammar, punctuation, and malformed Markdown;
   - awkward, unclear, or unnatural wording;
   - terminology, tone, formatting, and rule inconsistencies within or across the named files;
   - ambiguous, contradictory, redundant, or missing instructions;
   - correctness, security, reliability, or material inference-cost problems introduced or exposed by the changes.
5. If the user explicitly requests placeholder-contract review, trace the named prompts through
   `server/src/utils/prompt.ts` and the relevant prompt unit tests.
6. Report findings in severity order. If there are no findings, state that explicitly.

## Review Standard

- Explain the concrete ambiguity, inconsistency, or operational impact of non-editorial findings.
- Assign editorial defects `Low` severity unless they materially change meaning.
- Do not expand the review into unchanged runtime contracts, schemas, jobs, or tests unless the requested scope requires
  them.

## Output

For each finding, provide:

- severity: Critical, High, Medium, or Low;
- a concise title;
- the prompt file and exact line;
- the problem and its impact;
- a focused recommendation.

After the findings, list the named files, review baseline, and checks performed. Keep unresolved questions separate from
findings.
